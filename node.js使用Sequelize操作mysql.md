# node.js使用Sequelize操作mysql

## 1.安装

```nginx
npm install --save sequelize 
npm install --save mysql2
```

## 2.连接数据库

```javascript
const Sequelize = require('sequelize');

const sequelize = new Sequelize('test', 'root', '658575', {
    host: 'localhost',
    dialect: 'mysql',
    port:'3306'
})

sequelize
  .authenticate()
  .then(() => {
    console.log('连接成功');
  })
  .catch(err => {
    console.error('Unable to connect to the database:');
  });
```

## 3.创建模型

### 表约束

- timestamps：是否给每条记录添加 createdAt 和 updatedAt 字段，并在添加新数据和更新数据的时候自动设置这两个字段的值，默认为true
- paranoid：设置 deletedAt 字段，当删除一条记录的时候，并不是真的销毁记录，而是通过该字段来标示，即保留数据，进行假删除，默认为false
- freezeTableName：禁用修改表名; 默认情况下，sequelize将自动将所有传递的模型名称（define的第一个参数）转换为复数。 默认为false        
- tableName：手动设置表的实际名称
- 定义表索引  indexes：Array<Object>

### 字段约束

- type：字段类型，String|DataTypes
- allowNull：是否允许为空，默认为true
- defaultValue：默认值，默认为null
- unique：值唯一，默认为false
- primaryKey：是否为主键，默认为false

```java
// 导入创建模型需要的函数
const { Sequelize, sequelize } = require('../init')
// 创建模式 第一个参数是 表名
const User = sequelize.define('user', {
    // 一个对象就是一个字段
    username: {
        // 约束字段的类型
        type: Sequelize.STRING,
        // 是否允许为空 默认是 true
        allowNull: false,
    },
    password: {
        type: Sequelize.STRING,
        // 是否允许为空 默认是 true
        allowNull:false
    }
})
```

### 4.查询数据

```javascript
// 查找所有用户
// users是查询成功返回的 JavaScript 值
User.findAll().then(users => {
    // 转换为 json 字符串
    console.log("All users:", JSON.stringify(users, null, 4));
});
```



### 5.创建用户

```javascript
// 创建新用户
// 创建成功返回 用户对象 
User.create({ username: "admin", password:"123456"}).then(user => {
    console.log('创建成功！'+ user.username);
});  
```

### 6.修改用户信息

```javascript
// 将所有 password 为 123456
User.update({ password: "123456" }, {
    where: {
        password: ''
    }
  }).then(() => {
    console.log("修改成功");  
});
```



### 7.删除用户

```javascript
// 删除所有名为“admin”的人
User.destroy({
    where: {
      username: 'admin'
    }
  }).then(() => {
    console.log("删除所有名为admin的用户成功");
  });
```

