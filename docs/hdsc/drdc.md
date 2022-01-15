### 导出前端调用实现
```
// 查询参数 queryParams
queryParams: {
  pageNum: 1,
  pageSize: 10,
  userName: undefined
},

// 导出接口exportUser
import { exportUser } from "@/api/system/user";

/** 导出按钮操作 */
handleExport() {
  const queryParams = this.queryParams;
  this.$confirm('是否确认导出所有用户数据项?', "警告", {
	  confirmButtonText: "确定",
	  cancelButtonText: "取消",
	  type: "warning"
	}).then(function() {
	  return exportUser(queryParams);
	}).then(response => {
	  this.download(response.msg);
	}).catch(function() {});
}
```

### 导出后端实现
以系统功能中的 "系统管理 - 参数设置" 导出 为例
```
    /* 导出 */
    @Post("export")
    @RequiresPermissions('system:config:export')
    @Keep()                                                                         //保持原数据返回的装饰器
    async export(@Body(PaginationPipe) reqConfigListDto: ReqConfigListDto) {
        const { rows } = await this.sysConfigService.list(reqConfigListDto)         //分页数据查询
        const file = await this.excelService.export(SysConfig, rows)                //生成buffer文件
        return new StreamableFile(file)                                             //返回文件
    }
```
实体类中进行注解
```
    export class SysConfig extends BaseEntity {
    /* 参数主键 */
    @PrimaryGeneratedColumn({
        name: 'config_id',
        comment: '参数主键'
    })
    @IsNumber()
    @Excel({                                                                        //导出的字段注解,参数配置可自己查看
        name: '参数主键'
    })
    configId: number
    ...
    }
```

### 导入前端调用实现
```
import { getToken } from "@/utils/auth";

// 用户导入参数
upload: {
  // 是否显示弹出层（用户导入）
  open: false,
  // 弹出层标题（用户导入）
  title: "",
  // 是否禁用上传
  isUploading: false,
  // 是否更新已经存在的用户数据
  updateSupport: 0,
  // 设置上传的请求头部
  headers: { Authorization: "Bearer " + getToken() },
  // 上传的地址
  url: process.env.VUE_APP_BASE_API + "/system/user/importData"
},

// 导入模板接口importTemplate
import { importTemplate } from "@/api/system/user";

/** 导入按钮操作 */
handleImport() {
  this.upload.title = "用户导入";
  this.upload.open = true;
},
/** 下载模板操作 */
importTemplate() {
  importTemplate().then(response => {
	this.download(response.msg);
  });
},
// 文件上传中处理
handleFileUploadProgress(event, file, fileList) {
  this.upload.isUploading = true;
},
// 文件上传成功处理
handleFileSuccess(response, file, fileList) {
  this.upload.open = false;
  this.upload.isUploading = false;
  this.$refs.upload.clearFiles();
  this.$alert(response.msg, "导入结果", { dangerouslyUseHTMLString: true });
  this.getList();
},
// 提交上传文件
submitFileForm() {
  this.$refs.upload.submit();
}
```


### 导入后端实现
以系统功能中的 "系统管理 - 用户管理" 导入 为例
```
import { storage } from 'src/modules/common/upload/upload.module';
@Module({
imports: [
    TypeOrmModule.forFeature([User]),
    forwardRef(() => RoleModule),
    PostModule,
    DeptModule,
    MulterModule.register({                         //这里进行上传文件配置
        storage: storage,
        preservePath: false,
    })
],
controllers: [
    UserController,],
providers: [
    UserService],
exports: [UserService]
})
export class UserModule { }
```
```
/* 用户导入 */
@Post('importData')
@RequiresPermissions('system:user:import')
@UseInterceptors(FileInterceptor('file'))                               //配置上传接收
async importData(@UploadedFile() file: Express.Multer.File) {
    const data = await this.excelService.import(User, file)             //获取导入文件的对象数组
    await this.userService.insert(data)
}
```