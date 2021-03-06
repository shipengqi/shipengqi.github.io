# Axios 使用


`Axios` 是一个基于 `promise` 的 HTTP 库，同时支持浏览器和 Node.js，可以用来替代 ajax 请求。

<!--more-->

## 简单使用

### 安装

```bash
npm install axios

# 或者
yarn add axios

# cdn
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

### API

```javascript
axios(options)

// 或者
axios(url[, options])

// 或者使用请求方法别名
axios.request(options)
axios.get(url[, options])
axios.delete(url[, options])
axios.head(url[, options])
axios.post(url[, data[, options]])
axios.put(url[, data[, options]])
axios.patch(url[, data[, options]])
```

> 在使用别名方法发送请求时， `url`、`method`、`data` 这些属性都可以不在配置中指定。

#### options

options: `Object`, 创建请求时的配置选项。

```javascript
{
  // `url` 是用于请求的服务器 URL，必需的选项。
  url: '/users',

  // `method` 是创建请求时使用的方法
  method: 'post', // 默认是 get

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://demo:8080/api/',

  // `transformRequest` 钩子函数 before request，允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data) {
    // 对 data 进行任意转换处理

    return data;
  }],

  // `transformResponse` 钩子函数 after response，在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理

    return data;
  }],

  // `headers` 设置请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` 与请求一起发送的 URL 参数，比如下面的例子中的id会转化为`https://demo:8080/api/users?id=123`
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    id: 123
  },

  // `paramsSerializer` `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `data` 是作为请求主体被发送的数据
  // 只适用于 'PUT', 'POST', 和 'PATCH' 方法
  // 在没有设置 `transformRequest` 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fred'
  },

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,

  // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // 默认的

  // `adapter` 自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应.
  adapter: function (config) {
    /* ... */
  },

  // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization` 头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

  // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // 默认的

  // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` 是承载 xsrf token 的值的 HTTP 头的名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // 默认的

  // `onUploadProgress` 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

  // `maxContentLength` 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // 默认的
  },

  // `maxRedirects` 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为0，将不会 follow 任何重定向
  maxRedirects: 5, // 默认的

  // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // `keepAlive` 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 'proxy' 定义代理服务器的主机名称和端口
  // `auth` 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 `Proxy-Authorization` 头，覆写掉已有的通过使用 `header` 设置的自定义 `Proxy-Authorization` 头。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: : {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken` 指定用于取消请求的 cancel token
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

#### 并发请求

```javascript
axios.all([])
axios.spread(callback)


//example
function getUsers() {
  return axios.get('/users');
}

function getChannels() {
  return axios.get('/channels');
}

axios.all([getUsers(), getChannels()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));
```

#### 创建 axios 实例

```javascript
var axiosInstance = axios.create(options);
```

使用 `axios.create` 方法创建的实例，和直接使用 axios 的使用方法是一样的。

### 响应结构

axios 对服务端的响应进行了封装：

```javascript
{
  // `data` 服务端返回的响应数据
  data: {},

  // `status` 服务端响应的 HTTP 状态码
  status: 200,

  // `statusText` 服务端响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务端响应的头
  headers: {},

  // `config` 该请求的所有配置选型，一般可以通过这个 config 拿到请求 options 中的一些信息，比如 url,data 等
  config: {}
}

// example 请求成功
axios.get('/users')
  .then(function(response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```

### 配置默认选项

#### 全局配置默认选项

```javascript
axios.defaults.baseURL = 'https://demo';
axios.defaults.responseType = 'blob';
```

#### 配置实例默认值

```javascript
var instance = axios.create({
  baseURL: 'https://demo'
});
instance.defaults.responseType = 'blob';
```

### 配置的优先级

前面已经知道，axios 的可以在发送请求时配置 options，创建实例时可以配置 options，还有默认的 options，所以配置的合并有优先级。

`lib/defaults.js` 库的默认值  ->  实例的 defaults 属性  ->  发送请求的 options 参数
**也就是发送请求时的 options 参数会覆盖前两者的 options**。

### 拦截器

拦截器与 `transformRequest`,`transformResponse` 函数的作用有些类似，都是在发送请求前，和收到服务端响应后，做一些处理。

```javascript
// 请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });


// 响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

一个请求拦截器可以用来添加token的例子：

```javascript
function getToken() {
 // ...
}
axios.interceptors.request.use(function (config) {
    let token = getToken();
    if (token) {
       config.headers['Authorization'] = token;
    }
    //处理url
    //config.url
  }, function (error) {
    return Promise.reject(error);
  });
```

#### 移除拦截器

```javascript
var interceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(interceptor);
```

## 实现文件下载

```javascript
let opts = {
  responseType = 'blob';
  transformResponse: [async function (data) {
    let resBlob = data;
    let resData = null;
    try {
      let resText = await new Promise((resolve, reject) => {
        let reader = new FileReader();
        reader.addEventListener('abort', reject);
        reader.addEventListener('error', reject);
        reader.addEventListener('loadend', () => {
          resolve(reader.result);
        });
        reader.readAsText(resBlob);
      });
      resData = JSON.parse(resText) // try to parse as json eventually
    } catch (err) {
      // ignore
    }
    if (resData) {
      if (resData.error) {
        return null;
      } else {
        return data;
      }
    } else {
      fileDownload(resBlob, filename);
    }
  }]
}
let fetch = axios.create(opts);
fetch.get('http://demo/api/download')
.catch(error => {
  //todo
})
```

上面通过 axios blob 实现下载，能够处理 JSON 和 文件流 的通用逻辑，这里设置 `responseType` 为 `blob`，所有响应先转换为 `blob` 数据。
处理响应数据的逻辑也可以放到拦截器里。还可以通过设置 `onDownloadProgress` 函数实现下载进度条。

## 实现文件上传

axios 实现文件上传就简单的多：

```javascript
var self = this
let file = e.target.file
let formData = new FormData()
formData.append('file', file, file.name)
// 添加请求头
axios({
  url: 'http://demo/api/upload',
  method; 'post',
  data: formData,
  headers: {'Content-Type': 'multipart/form-data'}
})
.then(response => {
  console.log(response.data)
})
.catch(error => {
  console.error(error)
})
```

## 参考链接

- <https://www.kancloud.cn/yunye/axios/234845>
- https://www.zhihu.com/question/263323250/answer/267842980

