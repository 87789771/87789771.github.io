### 后端数据权限实现
使用 装饰器@DataScope() 配合 装饰器@DataScopeSql()
```
 //分页查询用户为例

    /* 分页查询用户列表 */
    @Get('list')
    @DataScope()                                               //启用数据权限，参数是用户表和岗位表的别名(默认值是 user, dept)
    @RequiresPermissions('system:user:query')
    @ApiPaginatedResponse(User)
    async list(@Query(PaginationPipe) reqUserListDto: ReqUserListDto, @DataScopeSql() sataScopeSql: string) {        // 根据角色数据权限组合sql语句将存放在 sataScopeSql中
        return this.userService.list(reqUserListDto, null, null, sataScopeSql)
    }
```

```
        //service层在查询数据库的时候，如果sataScopeSql存在就进行sql语句的拼接

        async list(reqUserListDto: ReqUserListDto, roleId?: number, reverse?: Boolean, sataScopeSql?: string): Promise<PaginatedDto<User>> {
        let where: FindConditions<User> = { delFlag: '0' }
        if (reqUserListDto.userName) {
            where.userName = Like(`%${reqUserListDto.userName}%`)
        }
        if (reqUserListDto.phonenumber) {
            where.phonenumber = Like(`%${reqUserListDto.phonenumber}%`)
        }
        if (reqUserListDto.status) {
            where.status = reqUserListDto.status
        }
        if (reqUserListDto.params) {
            where.createTime = Between(reqUserListDto.params.beginTime, moment(reqUserListDto.params.endTime).add(1, 'day').format())
        }
        const deptId = reqUserListDto.deptId ?? ''
        const queryBuilde = this.userRepository.createQueryBuilder('user')
        if (deptId) {
            queryBuilde.innerJoinAndSelect("user.dept", "dept", "concat('.',dept.mpath) like :v", { v: '%.' + deptId + '.%' })
        } else {
            queryBuilde.leftJoinAndSelect("user.dept", "dept")
        }
        if (roleId && !reverse) {
            queryBuilde.innerJoin("user.roles", "role", "role.roleId = :roleId", { roleId })
                .andWhere("role.delFlag = 0")
        }
        if (roleId && reverse) {
            queryBuilde.andWhere(qb => {
                const subQuery = qb.subQuery()
                    .select('user.userId')
                    .from(User, 'user')
                    .leftJoin('user.roles', 'role')
                    .where("role.roleId = :roleId", { roleId })
                    .getQuery()
                return "user.userId not in " + subQuery
            })
        }
        if (sataScopeSql) {                                                                                //重点就是这里，直接在最后拼接查询语句即可。
            queryBuilde.andWhere(sataScopeSql)
        }
        const result = await queryBuilde.andWhere(where).getManyAndCount()
        return {
            rows: result[0],
            total: result[1]
        }
    }
```