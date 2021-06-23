# Express框架

> Express是一个基于Node平台的web应用开发框架 它提供了一系列强大的特性 帮助你创建各种web应用 

```nginx
npm install express   
```

### Express框架特性

- 提供了方便简洁的路由定义方式
- 对获取HTTP请求参数进行了简化处理
- 对模块引擎支持程度高 方便渲染动态HTML页面
- 提供了中间件机制控制HTML请求
- 拥有大量第三方中间件对功能进行扩展

### Express创建服务器

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 访问请求
app.get('/', (req, res) => {
    // send()
    // 1.send()方法内部会检测响应内容的类型
    // 2.send()方法会自动设置http状态码
    // 3.send()方法会帮我们自动设置响应的内容类型及编码
    res.send('ok')
})

// 监听端口
app.listen(80)
console.log('已启动');
```

## 中间件

#### 什么是中间件

> 中间件就是一堆方法，可以接收客户端发来的请求，可以对请求做出响应，也可以将请求继续交给下一个中间件继续处理

![image-20200915083450088](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20200915083450088.png)

> 中间件主要由两部分构成，中间件方法以及请求处理函数
>
> 中间件方法由Express提供，负责拦截请求，请求处理函数由开发人员提供，负责处理请求
>
> 可以针对同一个请求设置多个中间件，对同一个请求进行多次处理
>
> 默认情况下，请求从上到下依次匹配中间件，一旦匹配成功，就终止匹配
>
> 可以调用next方法将请求的控制权交给下一个中间件，直到遇到结束请求的中间件为止

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 访问请求
app.get('/list', (req, res, next) => {
    req.name = "张三"
    next()
})
app.get('/list', (req, res) => {
    res.send(req.name)
})
// 监听端口
app.listen(80)
console.log('已启动');
```

#### app.use （接收所有请求）

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 接收所有请求
app.use((req,res,next) =>{
	console.log('接收到了所有请求');
		next()
})
// 监听端口
app.listen(80)
console.log('已启动');

```

#### 中间件应用

##### 1.路由保护，客户端在访问需要登陆的页面时，可以先使用中间件判断用户登陆状态，用户如果未登陆，则拦截请求，直接响应，禁止用户进入需要登陆的页面

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()

app.use('/admin',(req,res,next)=>{
	var isLogin = true
		// 如果用户已经登陆
	if(isLogin){
		// 让请求继续执行下去
		next()
		
	} else {
		// 如果用户没有登陆 直接对客户端做出响应
		res.send('你还没有登陆，请登陆')
	}
})

app.get('/admin',(req,res,next)=>{
	res.send('你已登陆')
})

// 监听端口
app.listen(80)
console.log('已启动');

```

##### 2.网站维护公告，在所有路由的最上面定义接收所有的请求的中间件，直接为客户端做出响应，网站正在维护中

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 接收所有的请求 直接做出响应 
// 因为没有调用next()方法 所以请求只到这里 没有继续下去
app.use((req,res,next)=>{
		res.send('网站正在维护')
})
app.get('/list',(req,res,next)=>{
		res.send('list')
})
app.get('/add',(req,res,next)=>{
		res.send('add')
})
// 监听端口
app.listen(80)
console.log('已启动');

```

##### 3.自定义404页面

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()

app.get('/list',(req,res,next)=>{
		res.send('list')
})
app.get('/add',(req,res,next)=>{
		res.send('add')
})

// 注意自定义404页面 这个路由 要才所有路由的最下面
// 因为路由时从上到下的 所以吧这个路由定义到最下面就可以实现 自定义404页面 效果
app.use((req,res) =>{
	// 为客户端响应404状态码以及提示信息
	res.status(404).send('页面找不到')
})

// 监听端口
app.listen(80)
console.log('已启动');

```

#### 错误处理中间件

> 在程序执行的过程中，不可避免的会出现一下无法预料的错误，比如文件读取失败，数据库连接失败，错误处理中间件是一个集中处理的地方。

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()

app.get('/index',(req,res,next) =>{
	// 创建一个错误 信息
	throw new Error('程序发生了未知错误')
})

// 错误处理中间件
app.use((err,req,res,next) =>{
	res.status(500).send(err.message)
})

// 监听端口
app.listen(80)
console.log('已启动');

```

### 构建模块化路由

##### app.js

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 导入路由对象
var home = require('./route/home')
var admin = require('./route/admin')

app.use('/home',home)
app.use('/admin',admin)

// 监听端口
app.listen(80)
console.log('已启动');
```

##### home.js

```js
// 导入express 框架
var express = require("express")
// 创建路由对象
var home = express.Router()

home.get('/index',(req,res)=>{
	res.send('欢迎来到home的index页面')
})
// 导出home路由对象
module.exports = home
```

##### admin.js

```js
// 导入express 框架
var express = require("express")
// 创建路由对象
var admin = express.Router()

admin.get('/index',(req,res)=>{
	res.send('欢迎来到admin的index页面')
})
// 导出admin路由对象
module.exports = admin
```

### GET参数的获取

> Express中框使用req.query既可获取GET参数，框架内部会将GET参数转换为对象并返回

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()

//接收 地址栏中我问号后面的参数 用 & 来隔开
// 例如： http://localhost/?name=xiaoke&age=18
app.get('/',(req,res) => {
	res.send('ok')
	// req.query 返回的是对象
	console.log(req.query) //{ name: 'xiaoke', age: '18' }
})

// 监听端口
app.listen(80)
console.log('已启动');
```

### POST参数的获取

> Express中接收POST请求的参数需要借助第三方包 body-parser

```nginx
npm install body-parser
```

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 导入body-parser模块
var bodyParser = require("body-parser")
// 配置body-parser模块
// extended: false 方法内部使用querystring模块处理请求参数的格式
// extended: true  方法内部使用第三方qs模块处理请求参数的格式
app.use(bodyParser.urlencoded({extended:false}))

app.post('/add',(req,res) =>{
	// req.body接收POST参数 并且响应到客户端
	res.send(req.body)
})
// 监听端口
app.listen(80)
console.log('已启动');
```

### Express路由参数

```js
// 导入express 框架
var express = require("express")
//创建网站服务器
var app = express()
// 导入body-parser模块
var bodyParser = require("body-parser")

	// 多个参数  如果多个参数 请求的时候要全部请求到 才能响应
app.get('/index/:id/:name/:age',(req,res,next) =>{
	// 接收POST请求参数
	res.send(req.params)
})

// 监听端口
app.listen(80)
console.log('已启动');

```

### 静态资源的处理

> 提供Express内置的express.static可以方便地托管静态资源文件，例如img css javascript 文件 等...

```js
app.use(express.static('静态资源文件夹'))
```

### 模块引擎

> 为了使art-template模板引擎能够更好的和Express框架配合，模板引擎官方在原art-template模板引擎的基础是封装了express-art-template
>
> 使用：npm install art-template express-art-template  命令下载

js

```js
var express = require("express")
var path = require('path')
var app = express()
// 第一个参数是模板的后缀  第二个是使用的模板引擎
app.engine('art',require('express-art-template'))
// 设置express框架模板的位置
app.set('views',path.join(__dirname,'views'))
// 设置express框架模板后缀
app.set('view engine','art')

app.get('/index',(req,res,next) =>{
	// 1.拼接模板路径
	// 2.拼接模板后缀
	// 3.哪一个模块和哪一个数据进行拼接
	// 4.将拼接结果响应给了客户端
	res.render('index',{
		name:'xiaoke'
	})
})

// 监听端口
app.listen(80)
console.log('已启动');
```

html

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title></title>
	</head>
	<body>
		<h1>{{name}}</h1>
	</body>
</html>
```


