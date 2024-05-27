<!--
 * @Author: JiangSheng 87789771@qq.com
 * @Date: 2024-05-21 10:56:06
 * @LastEditors: JiangSheng 87789771@qq.com
 * @LastEditTime: 2024-05-27 09:44:52
 * @FilePath: \docs\kslj\xmyx.md
 * @Description: 
 * 
-->
 ## 前期准备
 ```
node >= 18 (本人是最新的20.13.1)
npm >= 6.14.15
mysql >= 5.70
redis >= 3.0
 ```
**提示：**npm或yarn最好设置淘宝镜像，推荐使用yarn进行安装

## 运行系统
#### 后端运行
- 前往 github下载页面 ([槑槑管理系统](https://github.com/87789771/meimei-nestjs-admin)) 进行代码拉取。
- 拉去后里面包含 meimei-prisma-vue3（2.0） 和 meimei-typeOrm-vue2（1.0）， 我们使用 meimei-prisma-vue3 。里面包含Vue3的前端项目和nestjs的后端项目。
- 分别进入对应项目进行依赖包的安装。yarn 或者 npm install
- 创建数据库并命名 meimei-prisma （排序：utf8mb4_unicode_ci）
- 执行在后端程序根目录下执行 yarn db:g  （prisma根据模型层生成数据库脚本文件，可在prisma官网查询运行部署）
- 执行初始化数据库文件(根目录下的 meimei-prisma.sql)
- 修改数据库连接，编辑后端项目根目录下的 .env 文件。 DATABASE_URL 是数据库地址，SHADOW_DATABASE_URL是影子数据库地址（prisma开发阶段使用的，是个空库就行了）。
- 启动你的redis（该项目必须要使用redis）。
- 修改后端项目中 src/config/config.dev.ts 你可以在里面更改运行的端口，redis 连接等信息 （里面备注写的非常清楚）。
- 修改前端项目根目录下的 vite.config.ts 文件，修改 server 配前你需要的前端代理。
- 前后端均使用 npm dev 或者 yarn dev 进行运行。



#### 具体操作
- 修改数据库配置，编辑后端项目下 .env 文件
```
  # 文件下的内容会被nest-config自动加载进入环境变量中
  # 运行环境
    NODE_ENV="development"
  # NODE_ENV="production"
  # 操作的数据库   //  connection_limit=20设置20个连接池 ，pool_timeout=0 禁用连接池超时
  # 示例       mysql://账号:密码@ip:端口/数据库名称?connection_limit=连接池数量&pool_timeout=连接超时时间(0表示一直等待)
              # mysql://meimei:123456@127.0.0.1:3306/meimei?connection_limit=20&pool_timeout=0
  DATABASE_URL="mysql://meimei:123456@127.0.0.1:3306/meimei?connection_limit=20&pool_timeout=0"

  # 影子数据库是第二个临时数据库，每次运行 prisma migrate dev 时都会自动创建和删除*，主要用于检测问题，例如架构偏移或生成的迁移的潜在数据丢失。
  SHADOW_DATABASE_URL="mysql://test_db:123456@203.25.211.232:3306/test_db"
```
- 修改redis连接，编辑后端项目下 src/config/config.dev.ts
```
<!-- 缓存redis -->
  redis: {
    host: 'localhost',
    port: '6379',
    password: '123456',
    db: 0,
  },
```
- 修改前端代理，编辑前端项目下 vite.config.ts
```
    server: {
      port: 80,
      host: true,
      open: true,
      proxy: {
        // https://cn.vitejs.dev/config/#server-proxy
        "/dev-api": {
          target: "http://127.0.0.1:3000",
          changeOrigin: true,
          rewrite: (p) => p.replace(/^\/dev-api/, ""),
        },
      },
    },
```
