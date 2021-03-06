# 使用 Swagger 生成 API 文档


[Swagger](https://swagger.io/) 是目前最受欢迎的 REST API 文档生成工具之一。

<!--more-->

本文只介绍 [Koa](https://github.com/koajs/koa) 与 [Swagger](https://swagger.io/) 的集成。

## 安装依赖

安装 [koa2-swagger-ui](https://www.npmjs.com/package/koa2-swagger-ui) 。

```bash
npm install koa2-swagger-ui --save
```

## 配置

关于 Swagger UI 配置参考：

- [Swagger 从入门到精通](https://huangwenchao.gitbooks.io/swagger/content/)
- [swagger-ui](https://github.com/swagger-api/swagger-ui/blob/master/docs/usage/configuration.md)

## 集成

```javascript
const Koa = require('koa');
const koaSwagger = require('koa2-swagger-ui');

const app = new Koa();

app.use(
  koaSwagger({
    routePrefix: '/swagger',
    swaggerOptions: {
      url: 'http://petstore.swagger.io/v2/swagger.json', // example path to json
    },
  }),
);

app.listen(5000);
```

**OPTIONS**

- `routePrefix`， 默认是 `/docs`，访问 Swagger 的 url。
- `swaggerOptions`，Swagger UI 配置选项。
  - `url`，指定 Swagger API 的配置。

配置好之后可以直接通过 `http://localhost:5000/swagger` 访问。

