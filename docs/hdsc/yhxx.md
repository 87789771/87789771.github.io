### 后端获取当前请求用户的用户信息
 使用装饰器加管道进行实现    (@User   和   UserInfoPipe)
 ```
     /* 新增参数 */
    @Post()
    @RequiresPermissions('system:config:add')
    async add(@Body() reqAddConfigDto: ReqAddConfigDto, @User(UserEnum.userName, UserInfoPipe) userName: string) {       //将获取用户的用户名
        reqAddConfigDto.createBy = reqAddConfigDto.updateBy = userName
        await this.sysConfigService.addOrUpdate(reqAddConfigDto)
    }
 ```