### 前端调用实现
```
    // 一般在查询参数中定义分页变量
    queryParams: {
    pageNum: 1,
    pageSize: 10
    },

    // 页面添加分页组件，传入分页变量
    <pagination
    v-show="total>0"
    :total="total"
    :page.sync="queryParams.pageNum"
    :limit.sync="queryParams.pageSize"
    @pagination="getList"
    />

    // 调用后台方法，传入参数 获取结果
    listUser(this.queryParams).then(response => {
        this.userList = response.rows;
        this.total = response.total;
    });
```

### 后端实现
以系统功能中的 "系统管理 - 参数设置" 的分页查询 为例
```
 /* 分页查询参数列表 */
    @Get('list')
    @RequiresPermissions('system:config:query')
    @ApiPaginatedResponse(SysConfig)
    async list(@Query(PaginationPipe) reqConfigListDto: ReqConfigListDto) {    //使用  PaginationPipe 管道
        return await this.sysConfigService.list(reqConfigListDto)
    }

```
ReqConfigListDto 实体类继承 PaginationDto
```
export class ReqConfigListDto extends PaginationDto {
    ...
}
```