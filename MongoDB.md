# Mongodb命令

## MongoDB基本语法

```nginx
启动：net start mongodb
```

```nginx
登录：bin\mongo
```

```nginx
查看数据库：show databases
```

```nginx
选择数据库：use 数据库名称（如果选择不存在的数据库，mongodb不会报错，会隐式创建，当后期该数据库有数据时，系统会自动创建）
```

```nginx
删除数据库：1.通过use 选择数据库 2.通过db.dropDatabase() 删除当前选择的数据库
```

```nginx
查看集合：show collections
```

```nginx
创建集合：db.createCollection('集合名称')
```

```nginx
删除集合：db.集合名.drop()
```

## MongoDB运算符

```nginx
$gt 大于
```

```nginx
$gte 大于等于
```

```nginx
$lt 小于
```

```nginx
$lte 小于等于
```

```nginx
$ne 不等于
```

```nginx
$in in
```

```nginx
$nin not in
```

## MongoDB文档修改

#### 基础语法：

```nginx
db.集合名.update(条件,新数据[是否新增,是否修改多条])
```

> 是否新增：指条件匹配不到数据则插入(true或false) 默认是false
> 否修改多条：指匹配成功的数据都修改(true或false) 默认是false
> 基础语法会直接覆盖掉数据

#### 升级语法

```nginx
db.集合名.update({值:键},{修改器:{值:键}})
```

#### 修改器

> $inc 递增
> $rename 重命名列
> $set 修改列值
> $unset 删除列

#### MongoDB集合插入数据

##### 语法

```nginx
db.集合名.insert(JSON数据)
```

> 如果集合不存在系统会自动创建一个集合
> 自定义id： 直接覆盖就可以了

## MongoDB文档删除

#### 语法

```nginx
db.集合名.remove(条件,[是否删除一条])
```

> 是否删除一条trus是，false否默认

## MongoDB排序

#### 语法

```nginx
db.集合名.find().strt(JSON数据)
```

> 键-就是要排序的列字段 值：1升序 -1 降序

## MongoDB分页

#### 语法

```nginx
db.集合名.find().sort().skip(数字).limit(数字)
```

> sort()降序/排序 skip()跳过指定数量(可选) limit限制查询数量
>
> skip计算公式：(当前页-1)*每页显示多数

## MongoDB统计集合文档总数量

#### 语法

```nginx
db.集合名.find().count()
```

## MongoDB聚合查询

```nginx
db.集合名.aggregate( {管道:{表达式}])
```

> **常用管道**
> $group      将集合中的文档分组，用于添加结果
> $match      过滤数据，只要输出符合条件的文档
> $sort      聚合数据进一步排序
> $skip      跳过指定文档数
> $limit      限制集合数据返回文档数
> 常用表达式
>
> $sum      总和
> $sum:1      统计个数
> $avg      平均
> $min      最小值
> $max      最大值

## MongoDB索引

### 语法：

创建索引 

```nginx
db.集合名.createIndex(待创建索引的列[额外选项])
```

> 参数：待创建索引的列:{键:1,...键:1}
> 说明:1升序 -1降序 例如{age:1}表示创建age索引并按照升序的方式存储，额外选项：设置索引的名字或者唯一索引等等

删除索引

删除全部

```nginx
db.集合名.dropIndexes()
```

删除指定

```nginx
db.集合名.dropIndex(索引名)
```

查看索引

```nginx
db.集合名.getIndexes(索引名)
```

## MongoDB权限机制

### 创建账号

```nginx
db.creatUser({
    "user":"账号",
    "pwd":"密码",
    "roles":[{
        role:"角色"，
        db:"所属数据库"
    }]
})
```

#### 角色

- 超级用户角色：root
- 数据库用户角色：read   readWrite
- 数据库管理角色：dbAdmin   userAdmin
- 集群管理角色：clusterAdmin   clusterManager   clusterMonitor   hostManager
- 备份管理角色：backup   restore
- 所有数据库角色：readanyDatabase   readWriteAnyDatabase   userAdminAnyDatabase   DBAdminAnyDatabase



### mongoDB数据库导入数据

```nginx
mongoimport -d 数据库名 -c 集合名 --file 要导入的数据文件
```

> 注意 要使用 mongoimport  必须在电脑环境变量Path中添加 mongoimport  变量
>
> 路径是MongoDB的安装目录下的 bin文件 里面

## mongoodb数据库添加账号

1. 以系统管理员的方式运行CDM

2. 连接数据库 mongo

3. 查看数据库 show dbs

4. 切换admin数据库 use admin

5. 创建超级账号 db.createUser ({user:"root",pwd:"root",roles:["root"]})

6. 创建普通账号 use blog  db.createUser ({user:"blog",pwd:"blog",roles:["readWrite"]})

7. 停止服务 net stop mongodb

8. 卸载服务 mongod --remove

9. 创建服务

   ```nginx
   mongod --logpath="C:\Program Files\MongoDB\Server\4.2\log\mongod.log" --dbpath="C:\Program Files\MongoDB\Server\4.2\data" --install --auth
   ```

10. 启动mongoDB服务 net start mongoDB

11. 在项目中使用账号连接数据库

    ```nginx
    mongoose.connect(mongodb://用户名:密码@localhost:端口/数据库名) // 端口默认是27017
    ```

    



# Mongoose API

## 数据库连接

> 使用mongoose提供的connect方法既可连接数据库

### 语法：

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称 
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })
```

## 创建集合

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })

// 设定集合规则
var coursesSchema = new mongoose.Schema({
    name: String,
    age: Number,
    sex: String
})
// model创建集合 并应用规则 集合的名称一个字母是大写
var Course = mongoose.model('Course', coursesSchema) //courses
```

## 添加数据

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })

// 设定集合规则
var coursesSchema = new mongoose.Schema({
    name: String,
    age: Number,
    sex: String
})
// model 创建集合并应用规则 集合的名称一个字母是大写  
var Course = mongoose.model('Course', coursesSchema)

// 添加数据
// 定义要添加的数据
var course = new Course({
    name: "小柯2",
    age: 17,
    sex: "女"
})
// save()添加到集合中
course.save()
	 // .then() 创建成功的回调函数 
    .then(function (res) {
        console.log(res);
    })

```

#### 第二种添加数据

```js
Course.create({
    name: "xiaoke", age: 18, sex: "男"
})
    .then(dos => { console.log(dos); })
    .catch(err => { console.log(err); })
```

## 查询文档

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })
// 设定集合规则
var coursesSchema = new mongoose.Schema({
    name: String,
    age: Number,
    sex: String
})
// 创建集合并应用规则 集合的名称一个字母是大写
var Course = mongoose.model('Course', coursesSchema) //courses

// find()查询集合中所有文档
// 查询的文档 返回的都是数组
Course.find() // 括号里面可以添加条件  可以添加查询的字段 find().select('字段名 -_id') -_id 表示不查询这个字段
    .then(function (dos) {
        console.log(dos);
    })

// 返回一条文档 默认是集合的第一条 返回的类型是对象
// Course.findOne() // 括号里面可以添加条件
//     .then(function (dos) {
//         console.log(dos);
//     })
```

## 删除文档

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })
// 设定集合规则
var coursesSchema = new mongoose.Schema({
    name: String,
    age: Number,
    sex: String
})
// 创建集合并应用规则 集合的名称一个字母是大写
var Course = mongoose.model('Course', coursesSchema) //courses

// 括号里面填条件 如果是 {} 就是删除全部 
// 如果查询到多条匹配的文档 就删除的一个文档  
// 返回的是删除的数据
Course.findOneAndDelete({ name: "小柯2" }).then((dos => { console.log(dos) }))


// 删除多个 deleteMany 返回的是一个对象 n：是删除了多少条 ok：1 是执行成功
// Course.deleteMany({ name: "小柯2" }).then((dos => { console.log(dos) }))
```



### 条件查询

```js
// 匹配 大于 小于  
Course.find({ age: { $gt: 20, $lt: 50 } }).then((dos => { console.log(dos) }))
```

```js
// 匹配包含 
Course.find({ name: { $in: ['小'] } }).then((dos => { console.log(dos) }))
```

```js
// 选择要查询的字段 -_id 是不查询 _id 这个字段
Course.find.select('name age -_id').then((dos => { console.log(dos) }))
```

## 排序

#### 升序

```js
// 将数据安装年龄进行升序 sort()
Course.find().sort('age').then((dos => { console.log(dos) }))
```

#### 降序

```js
// 将数据安装年龄进行降序  - 
Course.find().sort('-age').then((dos => { console.log(dos) }))
```

## 分页

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })
// 设定集合规则
var coursesSchema = new mongoose.Schema({
    name: String,
    age: Number,
    sex: String
})
// 创建集合并应用规则 集合的名称一个字母是大写
var Course = mongoose.model('Course', coursesSchema) //courses


// skip 跳过多少条数据 limit 限制查询的数量
Course.find().skip(0).limit(3).then((dos => { console.log(dos); }))
```

## 修改文档

```js
// 导入mongoose模块
var mongoose = require('mongoose')

// mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
mongoose.connect('mongodb://localhost/taes')
    // then 是数据库连接成功的时候调用
    .then(function () {
        console.log('数据库连接成功');
    })
    // catch 是数据库连接失败的时候调用
    .catch(function (err) {
        console.log(err);
        console.log('数据库连接失败');
    })
// 设定集合规则
var coursesSchema = new mongoose.Schema({
    name: String,
    age: Number,
    sex: String
})
// 创建集合并应用规则 集合的名称一个字母是大写
var Course = mongoose.model('Course', coursesSchema) //courses
// 修改一条文档 括号里面的的一个参数 是查询条件  第二个是需要修改的列 返回的是 执行结果
Course.updateOne({ 查询条件 }, { 需要修改的值 }).then((dos => { console.log(dos); }))
//  参数和上面是一样 区别是 上面修改一个 现在修改匹配条件的全部
Course.updateMany({ 查询条件 }, { 需修改的值 }).then((dos => { console.log(dos); }))
```

## mongoose 验证规则

- required: true 必传 字段
- minlength: 3 字符串最小长度
- maxlength: 5 字符串最大长度
- trim: true 去掉 空格
- min:16 最小数值 不小于16
- max: 100 最大数值不超过100
- default: 如果没有传参数,就会使用这个默认自定义的 
- enum: [ ]  列举出当前字段可以拥有的值

```js
var coursesSchema = new mongoose.Schema({
    title: {
        type: String,
        required: [true, '自定义错误提示信息'],
        minlength: [3, '自定义错误提示信息'],
        maxlength: [5, '自定义错误提示信息'],
        trim: true
    },
    age: {
        type: Number,
        min: [16,'自定义错误提示信息'],
        max:[100,'自定义错误提示信息']
    },
    publisshDate:{
        type:Date,
        // Date.now 是当前的系统时间
        default:Date.now
    },
    category:{
    	type:String,
    	enum:['html','css','javascript']
    }
})
```

## 集合关联

> 通常不同集合的数据之间是有关系的,例如文章信息和用户信息存储不同的集合中,但文章是某个用户发表的,要查询文章的所有信息包括发表用户,就需要用到集合关联

#### 步骤

- 使用id对集合进行关联

- 使用populate方法进行关联集合查询

  ```js
  // 导入mongoose模块
  var mongoose = require('mongoose')
  // mongodb: 是协议 localhost 是地址 /taes 是数据库的名称
  mongoose.connect('mongodb://localhost/taes')
      // then 是数据库连接成功的时候调用
      .then(function () {
          console.log('数据库连接成功');
      })
      // catch 是数据库连接失败的时候调用
      .catch(function (err) {
          console.log(err);
          console.log('数据库连接失败');
      })
  // 用户集合规则
  var userSchema = new mongoose.Schema({
      name: {
          type: String,
          required: true
      }
  })
  // 文章集合规则
  var postSchema = new mongoose.Schema({
      title: {
          type: String
      },
      author: {
          type: mongoose.Schema.Types.ObjectId,
          ref: "User"
      }
  })
  // 用户集合
  var User = mongoose.model("User", userSchema)
  // 文章集合
  var Post = mongoose.model("Post", postSchema)
  // // 创建用户
  // User.create({ name: "itxiaoke" })
  //     .then((dos => { console.log(dos) }))
  // 创建文章
  // Post.create({ title: "IT", author: "5f5a0a1766f73b12844fbbd0" })
  //     .then((dos => { console.log(dos) }))
  // 联合查询
  Post.find().populate("author").then((dos => { console.log(dos) }))
  ```

  

