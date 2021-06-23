# node操作mysql

## 连接mysql

```javascript
// 导入操作mysql模块
var mysql = require('mysql');
// 定义 连接数据 默认端口 3306 如需要修改 port:端口
var connection = mysql.createConnection({
  host     : '47.107.105.83',
  user     : 'blog',
  password : 'blog',
  database : 'blog',
  charset:"utf8"
});
// 连接
connection.connect(function (err,dos) {
    if (err) {
        console.log('连接失败');
        return
    }
    console.log('连接成功');
});
```

## 查询数据

```javascript
// sql语句
var  sql = 'SELECT * from user where  id = 1';
// 第一个参数是sql语句 第二个是回调函数 里面的第一个参数是错误处理 第二个是查询的结果
connection.query(sql, function (err, result) {
    // 错误处理
        if(err){
          console.log('[SELECT ERROR] - ',err.message);
          return;
    }
    // 输出查询结果
       console.log(result); 
});
```

## 插入数据

```javascript
var addSql = "insert into test (sex,age) values('男',15)";
//增
connection.query(addSql,function (err, result) {
        if(err){
         console.log('插入失败',err.message);
         return;
        }        
       console.log('插入成功',result);          
});
 
```

## 修改数据

```javascript
var modSql = "update test set sex = '女', age = 18  where id  = 1";
//改
connection.query(modSql,function (err, result) {
   if(err){
         console.log('修改失败',err.message);
         return;
   }        
  console.log('修改成功',result.affectedRows);
});
```

## 删除数据

```javascript
var delSql = 'delete from test where id = 1';
//删
connection.query(delSql,function (err, result) {
        if(err){
          console.log('删除失败',err.message);
          return;
        }        
       console.log('删除成功',result.affectedRows);
});
```

