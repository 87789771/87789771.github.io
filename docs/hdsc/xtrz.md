### 后端实现系统日志

```
    /* 新增参数 */
    @Post()
    @Log({
        title: '参数设置',                                                      //模块标题
        businessType: BusinessTypeEnum.insert                                   //操作类型
    })
    @RequiresPermissions('system:config:add')
    async add(@Body() reqAddConfigDto: ReqAddConfigDto, @User(UserEnum.userName, UserInfoPipe) userName: string) {
        reqAddConfigDto.createBy = reqAddConfigDto.updateBy = userName
        await this.sysConfigService.addOrUpdate(reqAddConfigDto)
    }

    <!--   更多配置项 请自己查看
        export class LogOption {
            /* 操作模块 */
            title: string

            /* 操作功能 */
            businessType?: BusinessTypeEnum = BusinessTypeEnum.other

            /* 是否保存请求的参数 */
            isSaveRequestData?: boolean = true

            /* 是否保存响应的参数 */
            isSaveResponseData?: boolean = true
        }
     -->
```