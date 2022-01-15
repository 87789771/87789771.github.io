### 后端参数校验
在main.js下配置参数校验管道
```
    /* 全局参数校验管道 */
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,  // 启用白名单，dto中没有声明的属性自动过滤
    transform: true,   // 自动类型转换
  }))
```

校验规则使用语法请参考 [验证](https://docs.nestjs.cn/8/techniques?id=%e9%aa%8c%e8%af%81)