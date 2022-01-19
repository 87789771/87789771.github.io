### 防止连续提交
使用 @RepeatSubmit()进行接口装饰

```
      /* 新增公告 */
    @RepeatSubmit()                                      //防止连续提交
    @Post()
    @RequiresPermissions('system:notice:add')
    @Log({
        title: '通知公告',
        businessType: BusinessTypeEnum.insert
    })
    async add(@Body() reqAddNoticeDto: ReqAddNoticeDto, @User(UserEnum.userName, UserInfoPipe) userName: string) {
        reqAddNoticeDto.createBy = reqAddNoticeDto.updateBy = userName
        await this.noticeService.addOrUpdate(reqAddNoticeDto)
    }

```