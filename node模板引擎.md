## art-template模板引擎

#### 使用步骤

1. 在命令行工具使用 npm install art-template 命令进行下载
2. 使用 var template= require( ' art-template ' ) 引入模块引擎
3. 告诉模块引擎要拼接的数据和模板在哪里 var html = template( '模块路径 ' , 数据 ) 模板文件的后缀名是 art
4. 标准语法 ： {{ 名称 }}  原始语法： <%= 名称 %>
5. 原文输出 :  标准语法 ： {{ @ 名称 }}  原始语法： <%- 名称 %>    默认不是原文输出

##### javascript

```js
// 导入模板引擎
var template = require("art-template")
// 导入系统拼接字符串模块
var path = require("path")

// template是来拼接字符串的
// 1. 模块路径 要写绝对路径 __dirname
// 2.要在模块中显示数据 对象类型
var views = path.join(__dirname, 'views', 'index.art') // 使用path的joio方法 拼接字符串
var html = template(views, { // 返回的是拼接好的字符
    name: "xiaoke",
    age: 18
})


console.log(html);
```

##### HTML

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<!-- 标准语法 -->
<p>{{ name }}</p>
<p>{{age}}</p>
<!-- 简单的运算 -->
<p>{{ 1 +2 }}</p>

<!-- 原始语法 -->
<p> <%= name %> </p>
<p> <%= age %> </p>
<p> <%= 1+2 %> </p>

<body>

</body>

</html>
```

#### 条件判断 

```html
<!-- 条件判断 -->

<!-- 标准语法 -->
{{if age > 18}}
大于18
{{else if age < 15}}
小于15
{{else}}
如果前面的条件都不满足就显示我
{{/if}}

<!-- 原始语法 -->

<% if (age > 18 ) { %>
年龄大于18
<% } else if (age < 15) { %>
年龄小于15
<% } else { %>
如果前面的条件都不满足就显示我
<% } %>
```

#### 循环

```html
<!-- 循环 -->
<ul>
    {{each users}}
    <li>
        {{$value.name}}
        {{$value.age}}
        {{$value.sex}}
    </li>
    {{/each}}
</ul>
```

## 子模板

> 使用子模板可以将网站公共区块（头部 底部）抽离到单独的文件中 

- ###### 标准语法： {{include '模板路径'}}

- 原始语法：<% include('模板路径') %>

```html
<!-- 标准语法 -->

{{include './header.art'}}

<!-- 原始语法 -->

<% include('./header.art') %>
```

## 模板继承

> 使用模板继承可以将网站HTML骨架抽离到单独的文件中，其他页面模板可以继承骨架文件

![image-20200914081329233](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20200914081329233.png)

### 模块继承实例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>我是模板骨架</title>
    <!-- 预留的位置 -->
    {{block 'head'}} {{/block}}
</head>

<body>
	 <!-- 预留的位置 -->
	{{block 'content'}} {{/block}}
</body>

</html>
```

```js
<!-- index.art 首页模板 -->
<!-- 继承骨架模板 -->
{{extend '模板路径'}}
{{block 'head'}} <link rel="stylesheet" href="style.css"> {{/block}}
{{block 'content'}} <p>This is just an awesome page.</p> {{/block}}
```

## 模块配置

1. 向模板中导入变量 template.defaults.imports.变量名 = 变量值

2. 设置模块目录 template.defaults.root = 模块目录  

   > template.defaults.root = path.join(__dirname, 'views')

3. 设置模板默认的后缀 template.defaults.extname = ".art"

   

##### 实例1(日期格式)

```js
// 导入模板引擎
var template = require("art-template")
// 导入系统拼接字符串模块
var path = require("path")
var dateFormat = require("dateformat")


// 导入模板变量
template.defaults.imports.dateFormat = dateFormat
var views = path.join(__dirname, 'views', '05.art') // 使用path的joio方法 拼接字符串
var html = template(views, { // 返回的是拼接好的字符
    time: new Date
})


console.log(html);
```

```js
<!-- 日期格式化  -->
{{dateFormat(time,'yyyy-mm-dd-ss')}}
```

