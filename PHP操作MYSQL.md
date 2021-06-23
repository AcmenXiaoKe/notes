## PHP连接MYSQL

使用 PDO 

```php
<?php

$config = [
    // 地址
    'host'=>'127.1.1.1:3306',
    // 用户名
    'user'=>'root',
    // 密码
    'password'=>'658575',
    // 数据库名
    'database'=>'node_img',
    // 字符编码
    'charset'=>'utf8'
];
// 连接地址字符串
$dsn = sprintf("mysql:host=%s;dbname=%s;charset=%s",$config['host'],$config['database'],$config['charset']);

try{
    $pdo =  new PDO($dsn,$config['user'],$config['password']);
    // $pdo->query('select * from test;');
    // 返回值是 条数  发送 可以 增 删 改
    // echo $pdo->exec('insert into test(name,sex,age) values("xiaoke","男",17 );');
    
}
// 错误触发 
catch(PDOException $e) {
    die('数据库连接失败'.$e->getMessage());
}
?>
```

## 查询

```php
  $query = $pdo->query("select * from users;");
    // 查询全部数据  fetch(); 查询一条数据
   $rows = $query->fetchAll();
    // 将获取到的数据转换为json格式
    $json_encode = json_encode($rows,JSON_UNESCAPED_UNICODE);
    echo  $json_encode;
```

## 添加

```PHP
  $sql="insert into user (name,password) values('xioake','123');";
   $num=$pdo->exec($sql);//返回受影响的记录条数,num为int类型
   $insertid=$pdo->lastInsertId();//返回新增的主键ID
   if($num>0){
     echo '成功的添加了'.$num.'条记录,新增的主键ID是: '.$insertid;
   }else{
     echo '添加失败';
   }
```

## 修改

```php
   $sql="update user set name = 'xiaoke3'  where id = 3;";
   $stmt = $pdo->prepare($sql);
   $stmt->bindValue(1, 't1');
   $num = $stmt->execute();
   $count = $stmt->rowCount();//受影响行数
   echo '修改成功数量：'.$count;
```

## 删除

```php
   $sql="update user set name = xiaoke2  where id = 3;";
   $stmt = $pdo->prepare($sql);
   $stmt->bindValue(1, 't1');
   $num = $stmt->execute();
   $count = $stmt->rowCount();//受影响行数
   echo '删除操作影响行数：'.$count;
```

