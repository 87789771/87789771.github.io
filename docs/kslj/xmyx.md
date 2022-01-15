 ## 前期准备
 ```
node >= 14.17
npm >= 6.14.15
mysql >= 5.70
redis >= 3.0
 ```
**提示：**npm或yarn最好设置淘宝镜像，推荐使用yarn进行安装

## 运行系统
#### 后端运行
- 前往 github下载页面 ([https://github.com](https://github.com)) 进行代码拉取。
- 创建数据库mei-mei,执行初始化数据库文件(根目录下的 mei-mei.sql)
- 修改数据库连接，编辑 src/config/config.development.ts
```
  database: {
    type: 'mysql',
    host: process.env.MYSQL_HOST || 'localhost',                //数据库地址
    port: process.env.MYSQL_PORT || 3306,                       //数据库端口
    username: process.env.MYSQL_USERNAME || 'root',             //数据库账号
    password: process.env.MYSQL_PASSWORD || '123456',           //数据库密码
    database: process.env.MYSQL_DATABASE || 'mei-mei',          //数据库名称
    autoLoadModels: true,
    synchronize: true,
    logging: false,
  },
```
- 修改redis连接，编辑 src/config/config.development.ts
```
<!-- 缓存redis -->
  redis: {
    config: {
      url: 'redis://localhost:6379/0'
    }
  },
```
```
<!-- 队列reids -->
  bullRedis: {
    host: 'localhost',
    port: '6379'
  }
```
- 执行命令运行
```
npm run start:dev
```


#### 前端运行
前往 github下载页面 ([https://github.com](https://github.com)) 进行代码拉取。
```
# 进入项目目录
cd meimei-ui

# 强烈建议使用yarn进行安装
npm install --registry=https://registry.npm.taobao.org

# 本地开发 启动项目
npm run dev

```
