### 前端调用实现
```
<input id="filePath" name="filePath" class="form-control" type="file">
```
```
function submitHandler() {
	if ($.validate.form()) {
		uploadFile();
	}
}

function uploadFile() {
	var formData = new FormData();
	if ($('#filePath')[0].files[0] == null) {
		$.modal.alertWarning("请先选择文件路径");
		return false;
	}
	formData.append('fileName', $("#fileName").val());
	formData.append('file', $('#filePath')[0].files[0]);
	$.ajax({
		url: prefix + "/add",
		type: 'post',
		cache: false,
		data: formData,
		processData: false,
		contentType: false,
		dataType: "json",
		success: function(result) {
			$.operate.successCallback(result);
		}
	});
}
```

### 后端实现
配置文件存放地址，我的在根目录下的public/upload
```
export let storage = multer.diskStorage({
    // 配置上传文件夹
    destination: async (req, file, cd) => {
        let currentDate = moment().format('YYYY-MM-DD')
        let path = join(__dirname, `../../../../public/upload/${currentDate}`)
        try {
            // 判断是否有该文件夹
            await fs.promises.stat(path)
        } catch (error) {
            // 没有该文件夹就创建
            await fs.promises.mkdir(path, { recursive: true })
        }
        // 挂载文件存储的路径
        req.query.fileName = '/upload/' + currentDate
        cd(null, path)
    },
    // 配置上传文件名
    filename: (req, file, cd) => {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9)
        // 挂载文件存储的路径
        req.query.fileName = req.query.fileName + '/' + uniqueSuffix + '-' + file.originalname
        cd(null, uniqueSuffix + '-' + file.originalname)
    }
})

@Module({
    imports: [MulterModule.register({
        storage: storage,
        preservePath: false,
    })],
    controllers: [
        UploadController,],
    providers: [MulterModule],
})
export class UploadModule { }
```
上传实现
```
 /* 单文件上传 */
    @Post('upload')
    @UseInterceptors(FileInterceptor('file'))
    async uploadFile(@UploadedFile() file: Express.Multer.File, @Query('fileName') fileName) {
        return {
            fileName,
            originalname: file.originalname,
            mimetype: file.mimetype,
        }
    }
```
