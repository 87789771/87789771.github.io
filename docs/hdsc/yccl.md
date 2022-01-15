### 后端异常处理
1. 自定义异常使用
   ```
   /* 新增或更改 */
    async addOrUpdate(reqAddConfigDto: ReqAddConfigDto) {
        const sysConfig = await this.findByConfigKey(reqAddConfigDto.configKey, (reqAddConfigDto as SysConfig).configId)
        if (sysConfig) throw new ApiException('参数键值已存在，请更换')                 //抛出自定义异常
        return await this.sysConfigRepository.save(reqAddConfigDto)
    }
   ```

1. 全局异常过滤器会捕获各种异常，以你需要的数据格式进行返回
 ```
    @Catch()
    export class AllExceptionsFilter implements ExceptionFilter {
        catch(exception: unknown, host: ArgumentsHost) {
            const ctx = host.switchToHttp();
            const response = ctx.getResponse();
            const { status, result } = this.errorResult(exception)
            response.header('Content-Type', 'application/json; charset=utf-8');
            response.status(status).json(result);
        }

        /* 解析错误类型，获取状态码和返回值 */
        errorResult(exception: unknown) {
            const status =
            exception instanceof HttpException
                ? exception.getStatus()
                : HttpStatus.INTERNAL_SERVER_ERROR;

            const code =
            exception instanceof ApiException
                ? (exception as ApiException).getErrCode()
                : status;

            let message: string
            if (exception instanceof HttpException) {
            const response = exception.getResponse()
            message = (response as any).message ?? response
            } else {
            message = `${exception}`
            }
            return {
            status,
            result: AjaxResult.error(message, code)
            }
        }
    }
 ```