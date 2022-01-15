### 接口文档自动生成
  项目的接口文档地址会在项目启动后的控制台中打印，你可在setup-swagger.ts文件中进行更改。

- 返回分页数据 使用 @ApiPaginatedResponse()
  ```
    /* 分页查询参数列表 */
    @Get('list')
    @RequiresPermissions('system:config:query')
    @ApiPaginatedResponse(SysConfig)                         //生成返回分页查询的接口文档
    async list(@Query(PaginationPipe) reqConfigListDto: ReqConfigListDto) {
        return await this.sysConfigService.list(reqConfigListDto)
    }
  ```

- 返回 {data: any} 使用 @ApiDataResponse()
  ```
    /* 通过id查询参数 */
    @Get(":configId")
    @RequiresPermissions('system:config:query')
    @ApiDataResponse(typeEnum.object, SysConfig)             //生成返回一个对象的接口文档
    async one(@Param('configId') configId: number) {
        const sysConfig = await this.sysConfigService.findById(configId)
        return DataObj.create(sysConfig)                     //将结果包装成 {data:any}
    }
  ```

  详细使用规则可查看 [OPENAPI](https://docs.nestjs.cn/8/openapi)
  