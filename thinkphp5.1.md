# thinkphp 5.1

## 01.安装thinkphp

> 使用 composer 进行安装
>
> 如果不填版本号 默认是 最新版本

```
composer create-project topthink/think=5.1.* tp5
```

## 02.URL 解析

###  URL解析

1. Thinkphp 框架非常多的操作都是通过URL来实现的
2. http://serverName/index.php/模块/控制器/操作/参数/值...
3. index.php 为入口文件，在public目录的 index.php 文件
4. 模块在 application 目录下默认有一个 index 目录，这就是一个模块
5. 而在 index 目录下有一个 controller 控制器目录的 Index.php 控制器
6. Index.php 控制器的类名也必须是 class Index 否则会错误
7. 而操作就是控制器 class Index 里面的方法，比如：index/hello
8. 那么完整的形式为：public/index.php/index/index/index
9. 官方给的默认模块，默认控制器，默认操作都是 index , 所以出现四个 index
10. 而操作还给另给了一个参数的方法：hello 如下
11. 那么完整形式为：public/index.php/index/index/hello/name/Lee
12. 为了更加清晰的了解 URL 路径的执行过程，我们自己创建一个完全不重复的 URL
13. 在 application 目录创建一个 test 目录 (模块)
14. 在 test 模块下创建控制器目录 controller 并 创建 index.php (控制器)
15. 创建代码如下

```php
<?php
namespace app\test\controller;
class Test
{
    public function index($name = 'xiaoke')
    {
        return 'Hello：'.$name;
    }
}

```

16. 预览地址：http://tp5.xkbk.top/test/Test/index/name/xiaobai

### URL模型

1. 上个要点已经了解了URL所有的访问规则，通过创建 tets 模块更加了解
2. 如果 你的环境没有开启伪静态，那么 ThinkPHP 不支持 URL 重写
3. 没有开启 URL 重写，那只能使用 PATH_INFO 模式，如下
4. public/index.php?s=test/index/name/xiaobai

## 03.控制器定义

1. 控制器，即 controller 控制器文件存在 controller 目录下
2. 类名和文件名大小写保持一致，并采用驼峰命名
3. 继承控制器基类，可以更方便使用功能，但不是必须的
4. 系统也提供了其他的方式，在不继承的情况下完成相同的功能
5. 前面我们知道如果是一个单词，首字母大写，比如 class Index
6. URL 访问时直接 public/index 即可
7. 那么如果创建的时双字母组合，比如 HelloWorld
8. URL 访问时必须为 public/hello_world
9. 如果你想原样的方式访问 URL 则需要关闭配置文件中的自动转换

```php
"url_convert" => false
```

10. 此时，URL 访问可以为 public/Helloworld
11. 如果你想改变根命名空间 app 为其他，可以在根目录下创建 .env 文件
12. 然后写上配对的键值对即可，app_namespace=application

### 渲染输出

1. ThinkPHP 直接采用内方法 return 返回的方式自己输出

2. 使用 json 输出，直接输入用 json函数

   ```php
   $data =  [
        "name"=>"xiaoke",
         "age"=>18,
      ];
   return json($data);
   ```

3. 使用 view 输出模板，开启错误提示，可知然后创建模板

   ```php
   return view();
   ```

4. 默认的输出方式为 html 格式输出，如果返回的时数组，则会报错

5. 可以修改配置文件里默认输出类型，修改为 json

   ```php
   'default_return_type'    => 'html',
   ```

6. 一般来说，正常的页面都是 html 输出，用于模板，AJAX默认为 json

7. 如果继承了基类控制器 ( Controller ) ，那么可以定义控制器的初始化方法：initialize()

8. initialize () 方法会在调用控制器方法之前去执行

   ```php
       protected function initialize()
       {
           echo 'init';
       }
   ```

9. initialize () 方法不需要返回任何值，输出方法用 PHP 方式，return 

## 控制器操作

### 前置操作

1. 继承 Controller 类后可以设置一个 $beforeActionList 属性来创建前置方法

   ```php
   <?php
   namespace app\test\controller;
   use think\Controller;
   class Before extends Controller
   {
       protected $beforeActionList = [
         'first',
         // one 方法不执行不调用 second 前置
         'second' => ['except'=>'one'],
         // third 前置只能通过调用 one 和 two 方法触发
         'third' => ['only'=>'one,two']
       ];
       public function index() {
           return 'index';
       }
       public function one ()
       {
           return 'one';
       }
       public function two()
       {
           return 'two';
       }
       public function third ()
       {
           return 'third';
       }
       public  function  second ()
       {
           return 'second';
       }
       protected function first ()
       {
           echo 'first<br>';
       }
   }
   ```

   2. 此时，我们可以分别 URL  访问不同的方法来理解前置的触发执行

   ### 跳转和重定向

   1. Controller 类提供了两个跳转的方法，success ( msg,url ) 和 error ( msg ) 

      ```php
       public  function index ()
          {
              $this->success('你即将跳转到https://xkbk.top/','https://xkbk.top/');
              // 自动返回上一页
              // $this->error(msg);
          }
      ```

   2. 成功或者错误有一个固定的页面模板：thinkphp/tpl/dispatch_jump.tpl

   3. 在 app.php 配置文件中，我们可以修改跳转页面

      ```php
      // 默认跳转页面对应的模板文件
          'dispatch_success_tmpl'  => Env::get('think_path') . 'tpl/dispatch_jump.tpl',
          'dispatch_error_tmpl'    => Env::get('think_path') . 'tpl/dispatch_jump.tpl',
      ```

   ### 空方法和空控制器

   1. 当访问一个不存在的方法时，系统会报错，我们可以使用 _empty () 来拦截

      ```php
      public  function  _empty ($name)
          {
              return '不存在当前方法'.$name;
          }
      ```

   2. 当访问一个不存在的控制器时，系统也会报错，我们可以使用 Error 类来拦截；

      ```php
      class Error
      {
          public function  index (Request $request)
          {
              return  '当前控制器不存在!:' . $request->controller();
          }
      }
      ```

   3. 系统默认为 Error 类，如果需要自定义，则在 app.php 配置文件中修改

      ```php
      // 默认的空控制器名
          'empty_controller'       => 'Error',
      ```


## 数据库与模型

### 连接数据库

1. ThinkPHP采用内置抽象层将不同的数据库操作进行封装

2. 数据抽象基于 PDO 模式，无须针对不同的数据库编写相应的代码

3. 使用数据库的第一步，就是连接你的数据库

4. 在根目录的 config 下的 databases.php 可以设置数据库的连接信息

5. 大部分系统已经给了默认值，你只需要修改和填写需要的值即可

   ```php
   return [
       // 数据库类型
       'type'        => 'mysql',
       // 服务器地址
       'hostname'    => '127.0.0.1',
       // 数据库名
       'database'    => 'thinkphp',
       // 数据库用户名
       'username'    => 'root',
       // 数据库密码
       'password'    => '',
       // 数据库连接端口
       'hostport'    => '',
       // 数据库连接参数
       'params'      => [],
       // 数据库编码默认采用utf8
       'charset'     => 'utf8',
       // 数据库表前缀
       'prefix'      => 'think_',
   ]
   ```

6. type 属性默认支持的数据库有 mysql sqlite pasql sqlsrv

7. 配置完数据库，我们使用如下代码，在控制器端输出 mysql 里的数据

   ```php
   use think\Db;    
   public function getModelDate()
       {
           $data = Db::name('user')->select();
           return json($data);
       }
   ```

### 模型定义

1. 在 MVC 中，我们已经使用过 Controller (C) ，View ( V ) ，剩下一个就是 Model （M）

2. Model 即模型，就是处理和配置数据库的相关信息

3. 在项目应用的根目录创建model 文件夹 ，并且创建 User.php (表名和类名和文件名要一致)

   ```php
   <?php
   namespace app\test\model;
   
   use think\Model;
   
   class User extends  Model
   {
   
   }
   ```

4. 当创建了 User 模型了，控制器端可以这么写

   ```php
   public function getModelUser()
       {
           $data = User::select();
           return json($data);
       }
   ```

5. 而此时，命名空间会自动导入User模型：use app\test\model\User;

6. 很多时候，我们需要调式 SQL 是否正确，建议打开 Trace 可以查看原生 SQL

   ```php
    // 应用Trace
    'app_trace'              => true,
   ```

### 数据查询

1. Db::table() 中 table 必须指定完整数据表名 ( 包括前缀 )

2. 如果只是希望查询一条数据，可以使用 find() 方法

   ```php
   Db::table ('user')->find();
   ```

3. Db::getLastSql () 方法，可以得到最近一条 SQL 语句 查询的原生语句

   SELECT * FROM "user" LIMIT 1

4. 想指定数据查询，可以使用 where () 方法

   ```php
   Db::table('user')->where('id',3)->find();
   ```

5. 没有查询到任何数据就返回 null

6. 使用 findOrFail() 方法同样可以查询到一条数据，在没有数据时它会抛出一个异常

   ```php
   Db::table('user')->where('id',3)->findOrFail();
   ```

7. 使用 findOrEmpty() 方法同样可以查询到一条数据，在没有数据时它会返回一个空数组

   ```php
   Db::table('user')->where('id',1)->findOrEmpty();
   ```

8. 想要获取全部数据，我们可以使用 select () 方法

   ```php
   Db::table ('user')->select();
   ```

9. 查询全部数据，查询不到的时则返回空数组，使用 selectOrFail () 来抛出一个异常

   ```php
   Db::table ('user')->select()->selectOrFail();
   ```

10. 当在数据库配置文件中设置了前缀，那么我们可以使用 name () 方法来忽略前缀

    ```php
    Db::name ('user')->select()->selectOrFail();
    ```

11. ThinkPHP 提供了一个助手函数 db 可以更方便的来查询

    ```php
    \db('user')->select();
    ```

12. 通过 value() 方法，可以查询指定字段的值（单个)，没有数据返回null

    ```php
    \db('user')->where('id',3)->value('name');
    ```

13. 通过 column() 方法，可以查询指定字段的值（多个)，没有数据返回null

    ```php
    \db('user')->column('name');
    ```

14. 可以指定id为值得索引

    ```php
    Db::name('user')->column('name','id');
    ```

15. 使用 field() 方法，可以指定要查询的字段

    ```php
    $user->field('id,name,age')->select();
    ```

16. 使用 field () 方法，给指定的字段设置别名

    ```php
    $user->field('id,name as username')->select();
    $user->field(['id','name'=>'username'])->select();
    ```

17. 在 field() 方法里，可以直接给字段设置 MYSQL 函数

    ```php
    $user->field('id,SUM(price)')->select();
    ```

18. 对于更加复制的 MYSQL 函数，必须使用字段数组形式

    ```php
    $user->field(['id','LEET(email,5)'=>'leftemail'])->select();
    ```

19. 使用 field() 方法中字段排除，可以屏蔽掉那些不想显示的字段

    ```php
    $user->field('id,password',true)->select();
    ```

    

#### 更多查询

1. 如果我们多次使用数据库查询，那么每次静态创建都会生成一个实例，造成浪费

2. 我们可以吧对象保存下来，再进行反复调用即可

   ```php
   $user = Db::name('user');
   $data = $user->select();
   ```

3. 当同一个对象实例第二次查询后，会保留第一次查询得值

   ```php
   $data1 = $user->order('id','desc')->select();
   $data2 = $user->select();
   return Db::getLastSql();
   // SELECT * FROM 'user' ORDER BY 'id' DESC 
   ```

4. 使用 removeOption() 方法，可以清理上一次查询保留得值

   ```php
   $user->removeOption('where')->select();
   ```

#### 比较查询

1. 在查询数据进行筛选时，我们采用 where() 方法

   ```php
   $user->where('id',80)-select();
   $user->where('id','='80)->select();
   ```

2. where( 字段名,查询条件 )  where( 字段名,表达式,查询条件 )

3. 其中表达式不区分大小写，包括了比较、区间和时间三种类型的查询

4. 使用 <>、>、<、>=、<= 可以筛选出各种符合比较值的数据列表

   ```php
   $user->where('id','<>'80)->select();
   ```

#### 聚合查询

1. 使用 count () 方法可以求出所有数据的数量

   ```php
   $user->count();
   ```

2. count 可以设置指定id，比如有空值( null ) 的id，不会计算数量

   ```php
   $user->count('id');
   ```

3. 使用 max () 方法，求出所查询数据字段得最大值

   ```php
   $user->max('age');
   ```

4. 如果 max () 方法，求出得值不是数值，则通过第二参数强制转换

   ```php
   $user->max('age',true);
   ```

5. 使用 min () 方法，求出所查询数据字段得最小值

6. 如果 min () 方法，求出得值不是数值，也可以通过第二参数强制转换

7. 使用 avg () 方法，求出所查询字段数据得平均值

8. 使用 sum () 方法，求出所查询数据字段得总和

#### 子查询

1. 使用 fetchSql() 方法，可以设置不执行 SQL 而是返回SQL语句 默认 true

   ```php
   $user->fetchSql(true)->select();
   ```

2. 使用buidSql() ，也是返回SQL语句，但不需要再执行select()，而且有括号

#### limit

1. 使用 limit() 方法，限制获取输出数据的个数

   ```php
   $user->limit(5)->select();
   ```

2. 方页模式，即传递两个参数，比如从第2条开始显示到5套 limit(2,5)

   ```php
   $user->limit(2,5)->select();
   ```

#### page

1. page() 方页方法，优化了 limit() 方法，无须计算分页条数

   ```php
   // 第一页
   $user->page(1,5)->select();
   // 第二页
   $user->page(2,5)->select();
   ```

#### order

1. 使用 order() 方法，可以指定排序方法，没有指定第二参数默认是 asc

   ```php
   $user->order('id','desc')->select();
   ```

2. 支持数组的方式，对多个字段进行排序

   ```php
   $user->order(['create_time'=>'desc','price'='asc'])->select();
   ```

#### group

1. 使用 group () 方法，给性别不同的人进入 price 字段的总和统计

   ```php
   $user->field('gender','sum(price)')->group('group')->select();
   ```

   

### 数据新增

1. 使用 insert () 方法可以向数据库新增一条数据

   ```php
       public function increase()
       {
           $data = [
             "name"=> '真白',
             "age" => 15,
             "sex" => '女',
             "class" => '二年一班'
           ];
           $user = Db::name('user');
           // 新增数据 成功 返回 1
           $create = $user->insert($data);
           if($create) return '新增成功!';
       }
   ```

2. 你可以使用 data() 方法来设置添加的数据数组

   ```php
   Db::name('user')->data($data)->insert();
   ```

3. 如果你添加的一个不存在的字段，它会抛出一个异常

4. 如果你采用 mysql 数据库，它支持 REPLACE 写入

   ```php
   Db::name('user')->insert($data,true);
   ```

5. 使用 insertGetId() 方法，可以在新增成功后返回当前数据的 ID

   ```php
   // 新增成功 会当前数据的 ID
   $create = $user->insertGetId($data);
   ```

6. 使用 insertAll() 方法，可以批量新增数据，但是保持数据结构一致

   ```php
     public function insertAll()
       {
           $data = [
               [
                   "name"        =>      '玉子',
                   "age"         =>      16,
                   "sex"         =>      '女',
                   "class"       =>      '二年一班'
               ],
               [
                   "name"        =>      '北白川',
                   "age"         =>      15,
                   "sex"         =>      '女',
                   "class"       =>      '二年一班'
               ]
           ];
           // 返回值添加成功的数量
           $data =  Db::name('user')->insertAll($data);
           return $data;
       }
   ```

### 数据修改

1. 使用 update() 方法来修改数据，修改成功返回影响的行数，没有修改返回0

   ```php
           $data = [
               'name'=>'小柯'
           ];
           $user = Db::name('user');
           return $user->where('id',1)->update($data);
   ```

2. 或者使用 data() 方法传入要修改的数组，如果两边都传入会合并

   ```php
   $user->where('id',1)->data($data)->update(["age"=>15]);
   ```

3. 如果修改的数组中包括主键，那么可以直接修改

4. 使用 inc() 方法可以对字段增值，dec() 方法可以对字段减值

   ```php
   // 增
   $user->inc('price',1)->update();
   // 减    
   $user->dec('price',1)->update();       
   ```

5. 增值和减值如果同时对一个字段操作，前面一个会失效

6. 使用 exp() 方法可以在字段中使用 mysql 函数

   ```php
   $user->exp('email','UPPER(email)')->update($data);
   ```

7. 使用 raw() 方法修改更新，更加方便

   ```php
   $data = [
               'name'=>'xiaoke'
               'email'=>$user->raw('UPPER(email)'),
               'price'=>$user->raw('paice - 3')
    ];
    $user->where('id',1)->update($data);
   ```

8. 使用 setField () 方法可以更新一个字段

   ```php
   $user->where('id',1)->setField('name','小柯前端工程师');
   ```

9. 增值 setInc() 和减值 setDec() 也有简单的做法，方便更新一个字段值

   ```php
   $user->where('id',20)->setIce('price');
   ```

10. 增值和减值如果不指定第二个参数默认是 1

### 删除数据

1. 删除数据可以根据主键（可以删除多个）直接删除，删除成功返回影响的行数，否则0

   ```php
   // 多个
   $user->delete([5,6,7])
   // 一个
   $user->delete('1')
   ```

2. 正常情况下通过 where 方法来删除

   ```php
   $user->where('id',5)->delete();
   ```

3. 通过 true 参数删除表所有的数据。

   ```php
   $user->delete(true)
   ```

## 模型定义

1. 定义一个和数据库表匹配的模型

   ```php
   class User extends  Model
   ```

2. 模型会自动对应数据表，并且有自己的一套自己的命名规范

3. 模型需要去除表前缀，采用驼峰命名，并且首字母大写

   ```php
   tp_user   =>   User
   tp_user_type  =>  UserType
   ```

4. 如果担心设置的模型类名和PHP关键词冲突，可以开启应用类后缀

5. 在 app.php 中，设置 class_suffix 属性为 true 即可

   ```php
   'class_suffix'  => true
   ```

6. 设置完毕后，所有的控制器类名和模型类名需要加上 Controller 和 Model

### 设置模型

1. 默认主键为 id ， 你可以设置其他主键，比如说 uid

   ```php
   protected $pk = 'uid';
   ```

2. 从控制器端调用模型，如果和控制器类名重复，可以设置别名

   ```php
   user app\model\User as UserModel;
   ```

3. 在模型定义中，可以设置其他的数据表

   ```php
   protected $table = 'one'
   ```

4. 模型和控制器一样，也有初始化，在这里必须设置 static 静态方法

   ```php
   protected static function itin()
   {}
   
   ```

### 模型操作

1. 数据新增

   ```php
   $data = [
               "name"        =>      '玉子',
               "age"         =>      16,
               "sex"         =>      '女',
               "class"       =>      '二年一班'
           ];
   // 新增
   $result =  User::insert($data);
   ```

2. 数据删除

   ```php
   // 删除 也可以使用 destroy(id);
    $result = User::where('id','17')->delete();
   ```

3. 数据修改

   ```php
   $result = User::get(2);
   // 修改的字段
   $result -> name   = '小强';
   // 修改
   $result->save();
   ```

## 模板引擎

1. 模板引擎分为两种，一种是内置，一种是外置作为插件引入的，我们用内置的即可
2. 内置模板引擎的配置文件时 config/template.php
3. 默认情况下已经很好了，不需要修改任何参数，view_path 默认是 view 目录

### 模板函数

1. 控制器端先赋值一个变量，模板区设置MD5 加密操作

   ```php
   return  $this->fetch('index',['password'=>'123456']);
   // {$password|MD5}
   ```

2. 系统默认在编译的会采用 htmlentities 过滤函数 防止 XSS 跨站脚本 攻击

3. 如果你想换一个过滤函数，比如说 htmlspecialchars，可以在配置文件设置

   ```
   'default_filter'  =>  'htmlspecialchars'
   ```

4. 如果在某个字符，你不需要进行 HTML 实体转义的话，可以单独所有 raw() 处理

   ```php
   {$img['url']|raw}
   ```

5. 系统还提供了一些固定的过滤方法，如下

   | 函数    | 说明                                          |
   | ------- | --------------------------------------------- |
   | dete    | 格式化时间 { $time \| date = 'Y-m-d H:i:s ' } |
   | format  | 格式化字符串 {$ $number \| format = '%x' }    |
   | upper   | 转换大写                                      |
   | lower   | 转换小写                                      |
   | first   | 输出数组第一个元素                            |
   | last    | 输出数组最后一个元素                          |
   | default | 默认值                                        |
   | raw     | 不使用转义                                    |

6. 如果函数中，需要多个参数调用，直接逗号隔开即可

7. 模板中也支持多个函数进行操作，用 | 来隔开即可，函数从左到右依次执行

   ```php
   {$password | md5 | upper | substr=0,3}
   ```

8. 模板也可以实现三元运算，包括其他写法

   ```php
   {$name ? '正确' : '错误'} // $name为true返回正确，否则访问错误 	
   {$name ?= '正确'} // $name为true返回正确
   {$name ?? '不存在'} // ?? 用系统变量，没有值时输出
   {$name ?: '不存在'} // ?? 用普通变量，没有值时输出
   ```

### 模板标签

##### foreach 循环 

> $list 数据集 $key 索引 $item 数据对象

```php+HTML
{foreach $list as $key=>$item}
       <tr>
           <td>{$key}</td>
           <td>{$item.name}</td>
           <td>{$item.age}</td>
           <td>{$item.sex}</td>
           <td>{$item.class}</td>
       </tr>
{/foreach}
```

##### volist 循环

> name = 数据集 		id = 数据对象
>
> 和foreach不同的地方就时不需要 加$ name 和 id 

```php+HTML
{volist  name='list' id='item'}
    <tr>
        <td>{$key}</td>
        <td>{$item.name}</td>
        <td>{$item.age}</td>
        <td>{$item.sex}</td>
        <td>{$item.class}</td>
    </tr>
    {/volist}
```

##### for 循环

> for循环，顾名思义，通过起始值和终止值，结合步长实现循环

```php
{for start='1' end='100' comparison='<' step='2' name='i'}
{$i}	
{/for}
```

##### 比较标签

1. {eq}..{/eq}，比较两个值是否相同，相同即输出包容内容

   ```php
   $this->assign('username','xiaoke')
   {eq name='username' value='xiaoke'}
   小柯前端工程师
   {/eq}
   ```

2. 属性 name 是一个变量，$ 符可加可不加，而value里是一个字符串

3. 如果value 也需要一个是变量的话，那么value需要加上 $后的变量

4. {eq} 标签有一个别名标签，{equal}，效果是一样的

5. 相对应的 {neq}或{notequal}，实现相反的效果

   ```php
   {eq name='username' value='123'}
   两个值不相等的时候显示
   {/eq}
   ```

6. 这一组标签也支持 else操作，标签为：{else/}

   ```php
   {eq name='username' value='mr.lee'}
   两个值相等
   {else/}
   两个值不相等
   {/eq}
   ```

7. {gt} (>)、{egt} (>=)、{lt} (<)、{elt} (<=)、{heq} (===)、和 {nheq} (!==)

8. 除了相等和不相等，还有上面六种比较形式

   ```php
   {egt name='number' value='10'}
   大于10
   {else/}
   小于10
   {/egt}
   ```

9. 所有的标签都可以统一为 { compare } 标签来使用，增加一个 type 方法即可

   ```php
   {compare name='username' value='Mr.lee' type='eq'}
   两个值相等
   {/compare}
   ```

##### 定义标签

1. 如果你想在模板文件中定义一个变量，可以使用 {assgin} 标签

   ```php
   {assgin name='var' value='123'}
   {$var}
   ```

2. 定义常量

   ```php
   {define name='PI' value='3.1415'}
   {$Think.const.PI}
   ```

3. 有时，实在不知道在模板中怎么进行编码，可以采用 {php} 标签进行 原生编码

   ```php
   {php}
   	ehco '原生编码'; 
   {/php}
   ```

4. 要注意的是：原生编码就是 PHP 编码，不能再使用模板引擎的特殊编码方式

5. 比如 {eq}，{$user.name} 这些标签语法都不支持

6. 标签之间是支持嵌套功能的，比如从列表中找到 小白

   ```php
   {foreach $list as $key=>$item}
   {eq name='$item.name' value='小白'}
   {$item.name}
   {/eq}
   {/foreach}
   ```

##### 判断标签

###### switch

1. 使用 { switch } ... { /switch } 可以实现多个条件判断

   ```php
   {switch number}
   	{case 1} 1 {/case}
   	{case 5} 5 {/case}
   	{case 10} 10 {/case}
   	{default/} 没有
   {/switch}
   ```

2.  { case } 也支持多个条件判断，使用 | 隔开即可

   ```php
   {switch number}
   	{case 1|5|10} 数字 {/case}
   {/switch}
   ```

3.  { case } 后面也可以跟 变量，设置变量后不可以使用 |

###### if

1. 使用简单条件判断的 { if } 标签

   ```php
   {if $number > 10}
   大于10
   {/if}
   ```

   1. { if } 标签的条件判断可以使用 AND、OR等语法
   2. { if } 标签也支持 {else/} 语法
   3. { if } 标签也支持 {elseif} 多重判断

##### 范围标签

1. 范围标签：{ in } 和 { notin } ，判断值是否存在或不存在指定的数据列表

   ```php
   {in name='number' value='10,20,30,40'} 存在 {/in}
   {in name='number' value='10,20,30,40'}
   存在
   {else/}
   不存在
   {/in}
   ```

2. name 值是可以是系统变量，比如 $Think.xxx.yyy，value 可以是变量

3. 范围标签：{ between } 和 { notbetween }，判断值是否存在或不存在数据区间中

   ```php
   {between  name='number' value='10,20'} 是在这个范围中 {/between }
   {between  name='number' value='10,20'}
   是在这个范围中
   {else/}
   不是这个范围中
   {/between }
   ```

4. between 中的 value只能是2个值，表示一个区间，第三个值会无效

5. 区间不但可以表达数字，也可以是字母，比如：a-z、A-Z

##### 是否存在标签

1. 是否存在：{ present } 和 { notpresent  } 判断变量是否已经定义赋值（是否存在）

   ```php
   {present name='user'} 存在 {/present}
   {present name='user'}
   存在
   {else/}
   不存在
   {/present}
   ```

2. 是否为空：{empty} 和 {notempty} 判断变量是否为空值

   ```php
   {empty name='user'} 有值 {/empty}
   {empty name='user'}
   有值
   {else/}
   空
   {/empty}
   ```

3. 常量是否定义：{ defined } 判断常量是否定义 ( 是否存在 )

   ```
   {defined name='PI'}
   存在
   {else/}
   不存在
   {/defined}
   ```

##### 原样输出

```php
{literal}
变量标签形式：{$name}
{/literal}

输出结果： 变量标签形式：{$name}

```

##### 模板注释

```
{//$name}
{/*$name*/}
```



### 模板文件

1. 使用 { include } 标签来加载公共重复的文件，比如头部，尾部等等

2. 在模板 view 目录下创建一个 public 公共目录，分别创建 header 、footer、nav，html文件

   ```php+HTML
   // 注意逗号之后不能加空格
   {include file='public/header,public/nav'}
   index
   {include file='public/footer'}
   ```

3. 也可以包容一个文件的完整路径，包括后缀

   ```php+HTML
   {include file='../application/view/public/nav.html'}
   ```

4. 模板的标题和关键词，可以通过固定的语法进行传递

5. 对于标题，在控制器先设置一下标题变量，然后设置 { include } 设置属性

   ```php
   $this->assign('title','模板');
   {include file='public/header' title='$title' keyword='这是一个模板!'/}
   ```

6. 切换到 public/header.html 模板页面中，使用 [xxx] 的方式调用数据

   ```php+HTML
   <title>[title]</title>
   ```

### 静态文件

1. 有时，我们需要调用一下静态文件，比如 css/js 等等

2. 一般来说我们将这些静态文件存放在根目录的 public/static 中

3. 引入路径 /static/css/comment.css

   ```css
   <link rel="stylesheet" href="/static/css/common.css">
   ```

### 文件加载

1. 传递方式调用 CSS 或者 JS 文件时，采用 link 和 script 标签实现

2. 系统提供了更加智能的加载方式，方便加载 CSS 和 JS 等文件

3. 使用 { load } 标签和 href 属性来链接，不需要设置任何其它参数

   ```php
   {load href='/static/css/common.css'}
   {load href='/static/js/common.js'}
   ```

4. 也支持 href 多属性值的写法，如下

   ```css
   {load href='/static/css/common.css,/static/js/common.js'}
   ```

5. { load }还提供了两个别名 { js }、{ css } 来更好的实现可读性

   ```php
   {js href='/static/js/common.js'}
   {css href='/static/css/common.css'}
   ```

6. {js} 和 {css} 只是别名而已，识别 .js 或 .css 是根据后缀

## 视图渲染

1. 在控制器端，我们首先继承一下控制器基类 ( 不是必须，助手类也可以 )

2. 先采用第一种不带任何参数的最典型的做法（自动定位），看它报错信息

   ```php
   public function index ()
       {
           // 自动定位
           return $this->fetch();
       }
   ```

3. 模板路径为：当前模块/view/当前控制器名/当前方法(小写).html    /view/admin/login

4. 如果你想制定一个输出模板，可以在fetch () 方法传递相应的参数

   ```php
   return $this->fetch('edit'); //指定模板
   return $this->fetch('public/edit'); //指定目录下的模板
   return $this->fetch('admin@public/edit'); //指定模块下的模板
   return $this->fetch('/edit'); //view_path下的模板
   ```

5. 如果没有继承 Controller 控制器的话，可以使用助手函数 view() 方法

## 视图赋值

1. 在继承基类的情况下，我们可以使用 assign() 方法进行赋值

   ```php
   $this->assign('name','xiokae');
   ```

2. 也可以通过数组，进行多个变量赋值

   ```php
     $this->assign([
               'name'=>'xiaoke', //{$name}
               'age'=>18  // {$age}
      ]);
   ```

3. assign() 和 fetch() 方法也可以合二为一进行操作

   ```php
   return $this->fetch('index',['name'=>'xiaoke','age'=>18]);
   ```

4. 使用 display() 方法，可以不通过模板直接解析变量

   ```php
   $content = '{$name}.{$age}';
   return $this->display($content,['name']=>'xiaoke','age'=>18);
   ```

5. 使用 View::share() 静态方法，可以在系统任意位置做全局赋值

   ```php
   \think\fachde\View::share('key','value');
   ```

## 视图过滤

1. 如果需要对模板页面输出的变量进行过滤，可以使用 filter() 方法

   ```php
           $this->assign([
               'name'=>'小柯\n',
               'age'=>18
           ]);
           return $this->filter(function ($content){
               // 把 \n 替换成 <br/>
               return str_replace('\n','<br/>',$content);
           })->fetch('index');
   ```

2. 这里的$content 表示所有的模板变量，找到 \n 之后，实现换行

3. 如果控制器有 N 个方法需要过滤，可以在初始化中全局过滤

   ```php
   public function initialize ()
   {
       return $this->filter(function ($content){
               // 把 \n 替换成 <br/>
               return str_replace('\n','<br/>',$content);
           });
   }
   ```

## 变量输出

1. 当程序运行的时候，会在 runtime/temp 目录下生成一个编译文件

   ```php+HTML
   <?php /*a:1:{s:56:"/www/wwwroot/tp5/application/admin/view/login/index.html";i:1618968092;}*/ ?>
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
     <?php echo htmlentities($name); ?> <?php echo htmlentities($age); ?>
   </body>
   </html>
   ```

2. 如果传递的值是数组，那么编译文件也会自动相应的对应输出方式

   ```php
   $data['name'] = '小柯';
   $data['age']  =  15;
   $this->assign('user',$data);
   // 模板调用：{$user.name} 或 {$user['name']}
   // 编译文件：<?php echo htmlentities($user['name']); ?>
   ```

3. 如果传递的值是对象

   ```php
   ${obj->name}
   ```

4. 如果是模型对象的数据列表，数组和对象方式都可以

### 其他输出

1. 如果输出的变量没有值，可以直接设置默认值代替

   ```php
   {$user.name | default = '用户名不存在！'}
   ```

2. 使用 $Think.xxx.yyy 方式，可以输出系统变量;

3. 系统变量有 $_SERVER、$_ENV、$_GET、$_POST、$_REQUEST、$_SESSION、$_COOKIE

4. 可以使用 $Think.get.name 

   ```php
   // http://tp5.xkbk.top/admin/login?name=123
   {$Think.get.name} // 其他雷同
   {$Think.PHP_VERSION} // 获取系统版本
   {$Think.PHP_OS } // 获取系统类型
   // 123
   ```

5. 也可以输出系统 config 的 值

   ```php
   {$Think.config.app_debug}
   ```

## 模板继承

1. 模板继承是另一种布局方式，这种布局的思路更加灵活

2. 首先，我要创建一个 public/bese.html 的基模板文件，文件名随意

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="UTF-8">
       <title>{title}</title>
   </head>
   <body>
   </body>
   </html>
   ```

3. 创建一个新的方法 extend 载入新的模板 exend.html，然后加载基模板

   ```php
   {extend name='public/bese'}
   ```

4. 对于模板基类里的变量 {$title} 直接在控制器设置传值即可

## 路由

### 路由定义

1. 在没有定义路由规则的情况下，我们访问 /routes/article/ 包含 ID 的URL为

   ```php
   http://tp5.xkbk.top/routes/article/index/id/1
   或
   http://tp5.xkbk.top/routes/article/index/id/1.html
   ```

2. 将这个 URL 定义路由规则，在根目录 route 下 的 Route.php 里配置

   ```php
   // 第一个路由名字，第二个路由路径：模块/控制器/方法
   // :id 是动态传参,多个参数用/隔开
   \think\facade\Route::get('article/:id/:uid','routes/article/index/');
   ```

3. 当配置好规则后，会出现非法请求的错误，我们需要使用路由规则的 URL访问

   ```php
   http://tp5.xkbk.top/article/1
   ```

4. 在路由的规则表达式中，有多种地址的配置规则，具体如下

   ```php
   // \think\facade\ 添加这个前缀 phpStorm 有代码提示
   // 静态规则
   Route::get('/','index');
   // 静态动态结合
   \think\facade\Route::get('article/:id','routes/article/index/');
   // 多参数静态动态结合
   Route::get('article/:id/:uid','routes/article/index/');
   // 全动态地址，不限制是否 search 固定
   Route::get(':search/:id/:uid','search/index');
   // 包含可选参数地址
   Route::get('article/:id/[:uid]','routes/article/index/');
   // 规则完全匹配地址
   Route::get('search/:id/:uid$','search/search/');
   // 也可以开启完全匹配，在 app.php 中配置
   'route_complete_match'   => true,
   ```

5. 路由定义好之后，我们在控制器要创建这个路由地址，可以通过 url() 方法实现

   ```php
   // 不定义标识的做法
   return url('routes/article/index/',['id'=>10]);
   // 定义标识的做法
   \think\facade\Route::get('article/:id/[:uid]','routes/article/index/')->name('article');
    return url('article',['id'=>10]);
   ```

6. 路由也支持重定向功能，实现一个外部跳转

   ```php
   Route::get('details/:id','https://xkbk.top/:id')->status(302);
   Route::redirect('deails/:id','https//xkbk.top/:id',302)
   ```

   

### 变量规则

1. 系统默认的路由变量规则为 \w+，即字母、数字和下划线

2. 如果我们需要对具体的变量进行单独的规则设置，则需要通过 pattern() 方法

3. 将 details 方法里的 id 传值，严格限制必须是只能是数字 \d+

   ```php
   Route::get('details/:id','address/details')->name('det')->prattern('id','\d+');
   ```

4. 也可以设置 search 方法的两个值得规则，通过数组得方式传递参数

   ```php
   Route::get('search/:id/:uid','address/search')->name('search')->prattern(
   [
       'id'=>'\d+',
       'uid'=>'\d+'
   ]);
   ```

5. 以上两种，均为局部变量规则，也可以直接在 Route.php 设置全局变量规则

   ```php
   Route::pattern([
   'id'	=>	'\d+',
   'uid'	=>	'\d+'
   ])
   ```

6. 也支持使用组合变量规则方式，实现路由规则

   ```php
   Route::get('details-<id>','address/details')->pattern('id','\d+');
   http://localhost/details-11
   ```

7. 动态组合得拼装，地址和参数如果都是模糊动态的，可以使用如下方法

   ```php
   Route::get('details-:name-:id','Hello_:name/details')->pattern('id','\d+');
   ```

8. 在不设定任何规则的情况下，系统默认认为 \w+ ，在配置文件中可以更改默认配置规则

   ```php
   'default_route_pattern'		=>		'[\w\-]+'
   ```

### 闭包

1. 闭包支持我们可以通过 URL 直接执行，而不需要通过控制器和方法

   ```php
   Route::get('think', function () {
       return 'hello,ThinkPHP5!';
   });
   ```

2. 闭包支持也可以传递参数和状态规则

   ```php
   Route::get('hello/:name',function ($name){
       return 'hello '.$name;
   });
   ```

### 路由缓存

1. 路由缓存可以极大的提高性能，需要在部署环境下才有效果，在 app.php 开启

   ```php
   'route_check_cache'		=>		true
   ```

2. 为了测试路由缓存是否真的在起作用，可以通过命令来清理缓存测试

   ```nginx
   php think clear --route
   ```

### 路由参数

1. 设置路由的时候，可以设置第三个数组参数，主要实施匹配和检测和行为执行

2. ext 参数作用是检测 URL 的后缀，比如：我们强制所有 URL 后缀为 .html

   ```php
   Route::get('article/:id','index/article',['ext'=>'html|shtml']);  // 多个用 | 隔开
   ```

3. 第三数组参数也可以作用为对象的方法存在，比如改如下形式

   ```php
   Route::get('article/:id','index/article')->ext('html')
   ```

4. https 参数作用是检测是否为 https 请求，结合 ext 强制 html

   ```php
   Route::get('article/:id','index/article',['ext'=>'html','https'=>true]);
   Route::get('article/:id','index/article')->ext('html')->https();
   ```

5. 如果想让全局统一配置 URL 后缀的话，可以在 app.php 中配置

   ```php
   'url_html_suffix'		=>		'html'
   ```

6. denyExt 参数作用是禁止某些后缀的使用

   ```php
   Route::get('article/:id','index/article')->denyExt('gif|jpg|png')
   ```

7. filter 参数作用是对象额外参数进行检测

   ```php
   Route::get('article/:id','index/article')->filter('id',10);
   ```

8. model 参数作用是绑定到模型，第三参数设置 false 避免远程，也可以多参数

   ```php
   Route::et('article/:id','index/article')->model('id','/app/model/User);
   												... ->model('/app/model/User,false);
   Route::et('article/:id','index/article')->model(id&name,'/app/model/User');
   ```

9. option 参数作用是全局的路由进行配置，且可以多次调用

   ```php
   Route::option('ext,'html')->option('https','true')
   Route::get('article/:id','index/article')->option('ext,'html')->option('https','true')
   ```

### 路由分组

1. 使用 group() 方法，来进行分组路由注册

   ```php
   Route::group('col',[
   ':id'	=>	'Test/Collect/read',
   ':name'	=>	'Test/Collect/who'
   ])
   ```

2. 使用 group() 方法，并采用闭包的形式进行注册

   ```php
   Route::group('col',function(){
   	Route::get(':id','Test/Collect/read')
   	Route::get(':name','Test/Collect/who')
   });
   ```

3. 使用 prefix () 方法，简化路径的地址

   ```php
   Route::group('col',function(){
   	Route::get(':id','read')
   	Route::get(':name','who')
   })->prefix('Test/Collect/');
   ```

4. 使用append 方法，可以额外传入参数，用 request 获取

   ```php
   Route::group....->append('flag'=>1)
   ```

5. 路由规则（主要是分组和域名路由）定义的文件，加载时会解析消耗较多的资源

6. 尤其是规则特别庞大的时候，延迟解析开启让你只有在匹配的时候才会注册解析

7. 我们在 app.php 中开启延迟解析，然后通过 trace 来查看内存

   ```php
   'url_lazy_route'		=>		true
   ```

### 注解路由

1. 路由系统还提供了一个可以在注解（注释）中直接创建路由的方式，但默认是关闭的

2. 我们在 app.php 中 ，开启路由注解功能

   ```php
   'Route_annotation'		=>		true
   ```

3. 然后在控制器设置注解代码即可，可以使用 PHPDOS 生成一段，然后路由规则

   ```php
   /**
   * @param $id
   * @return string
   * @route('col:id')
   */
   ```

4. 第二参数，可以设置请求类型，而需要更多的规则，可以换行设置

   ```php
   /**
   * @param $id
   * @return string
   * @route('col:id')
   * ->ext('html')
   * ->pattern(['id'=>'\d+'])
   *
   */
   ```

5. 有几个注意点：语句结尾不需要 分号，路由规则结束后，需要有一个空行；

6. 支持资源路由

### MISS路由

1. 全局 MISS 类似开启强制路由功能，匹配不到相应规则时自动跳转到 MISS

   ```php
   \think\facade\Route::miss('index/Error/index');
   ```

2. 分组 MISS 可以在分组中使用 miss 方法，当不满足匹配规则时跳转到这里

### 跨域请求

1. 当不同域名进行跨域请求的时候，由于浏览器的安全限制，会被拦截

2. 所有，为了解除这个限制，我们提供路由 allowCrossDomain() 来实现

   ```php
   Route::get('article/:id','routes/article/index/')->name('article')->pattern('id','\d+')->allowCrossDomain();
   ```

3. 实现跨域比如没有实现的 header 头文件多了几条开头为 Access 的信息

4. 你可以使用添加或者修改 header 信息头

   ```php
   // 限制 https://xkbk.top 所有人 *
   Route::get('article/:id','routes/article/index/')->name('article')->pattern('id','\d+')->header('Access-Control-Allow-Origin','https://xkbk.top/')->allowCrossDomain();
   ```


### 路由的绑定

1. 路由绑定可以简化 URL 和路由规则的定义，可以绑定到 模块/控制器/操作

2. 由于本身不是规则，需要关闭强制路由来测试，本身绑定并不是定义路由

3. index模块/User控制器/read:    http://.../index/user/read/id/5

   ```php
   // 绑定路由到 index模块
   Route::bind('index');		http://.../user/read/id/5
   // 绑定路由到 User 控制器
   Route::bind('index/User');	http://.../read/id/5
   // 绑定路由到 read 操作
   Route::bind('index/User/read');	http://.../id/5
   ```

### 路由别名

1. 给一个控制器起别名，可以通过别名自动生成一系列规则

2. 比如说，给 index 模块下的 User 控制器创建别名：user，省去了模块 index

   ```php
   Route::alias('user','index/User');
   
   http://localhost:8000/user/create
   http://localhost:8000/user/edit/id/5
   ```

3. 也可以直接绑定到类，来实现相同的效果

   ```php
   Route::alais('user','\app\index\controller\User');
   ```

4. 也支持别名限制条件

   ```php
   Route::alias('user','index/User',['ext'=>'html']);
   Route::alias('user','index/User','')->ext('html');
   ```


### 路由的URL生成

1. 之前所有的URL，都是手动输入的，而路由也通过了一套生成的方法

   ```php
   Url::build('地址表达式',['参数'],['Url后缀'],['域名']);
   Url('地址表达式',['参数'],['Url后缀'],['域名']);
   ```

2. 使用 build() 方法，只传一个控制器时，会被误认为 Collect下的 index 方法

   ```php
   Url::build('Index');   //collect/index.html
   ```

3. 在没有设置路由的情况下，传递一个控制器以及操作方法

   ```php
   Url::build('Index/create')		// /index/create.html
   ```

4. 如果设置了对应的路由，那么生成的URL的会对应到路由

5. 下面是没有设置路由和设置路由的带参数的URL生成

   ```php
   Url::build('Index/read','id=5');    // blog/read/id/5.html
   								// /read/5.html
   ```

6. 参数部分，也可以用数组的方式，当然，多参数也支持

   ```php
   Url::build('Index/read',['id'=>5]);
   Url::build('Index/read','id=5&uid=10');
   Url::build('Index/'read',['id'=>5,'uid'=>10]);
   ```

7. 也可以使用助手函数 url 直接来设置

   ```php
   url('Index/read',['id'=>5]);
   ```

8. 也可以使用普通的地址来设置 url

   ```php
   Url::build('Index/read?id=5');
   ```

9. 也可以设置和路由规则配对的方式设置Url

   ```php
   Url::build('/read/5');
   ```

10. 使用 #name，可以生成一个带锚点的 url

    ```php
    url('Index/edid#name',['id'=>5]);
    ```

11. 使用 Url::root(' index.php ') 在 URL 前面加上一个 index.php

12. 但这个添加需要整体考虑路径是否支持或正确，否则无法访问

13. 在本身有 index.php 的时候，使用 Url::root('/') 隐藏

    ```php
    Url::root('/index.php')
    ```

## 数据请求

### 请求对象

1. 当控制器继承了控制器的基类时，自动会被注入 Request 请求对象的功能

   ```php
   class Rely extends Controller
   {
   	public function index()
   	{
   		return $this->request->param('name');
   	}
   }
   ```

2. Request 请求对象拥有一个 param方法，传入参数 name，可以得到相应得值

3. 如果我们不继承控制器基类，可是自行注入 Request对象，使用得是依赖注入

   ```php
   class Rely 
   {
   	public function index($request)
   	{
   		return $request->param('name');
   	}
   }
   ```

4. 使用助手函数 request () 方法也可以应用在没有依赖注入

   ```php
   class Rely 
   {
   	public function index($request)
   	{
   		return request->param('name');
   	}
   }
   ```

### 请求信息

| 方法        | 含义                                   |
| ----------- | -------------------------------------- |
| host        | 当前访问域名或者IP                     |
| scheme      | 当前访问协议                           |
| port        | 当前访问的端口                         |
| remotePort  | 当前请求的REMOTE_PORT                  |
| protocol    | 当前请求的SERVER_PROTOCOL              |
| contentType | 当前请求的CONTENT_TYPE                 |
| domain      | 当前包含协议的域名                     |
| subDomain   | 当前访问的子域名                       |
| panDomain   | 当前访问的泛域名                       |
| rootDomain  | 当前访问的根域名（`V5.1.6+`）          |
| url         | 当前完整URL                            |
| baseUrl     | 当前URL（不含QUERY_STRING）            |
| query       | 当前请求的QUERY_STRING参数             |
| baseFile    | 当前执行的文件                         |
| root        | URL访问根地址                          |
| rootUrl     | URL访问根目录                          |
| pathinfo    | 当前请求URL的pathinfo信息（含URL后缀） |
| path        | 请求URL的pathinfo信息(不含URL后缀)     |
| ext         | 当前URL的访问后缀                      |
| time        | 获取当前请求的时间                     |
| type        | 当前请求的资源类型                     |
| method      | 当前请求类型                           |

对于上面的这些请求方法，一般调用无需任何参数，但某些方法可以传入`true`参数，表示获取带域名的完整地址，例如：

```php
use think\facade\Request;
// 获取完整URL地址 不带域名
Request::url();
// 获取完整URL地址 包含域名
Request::url(true);
// 获取当前URL（不含QUERY_STRING） 不带域名
Request::baseFile();
// 获取当前URL（不含QUERY_STRING） 包含域名
Request::baseFile(true);
// 获取URL访问根地址 不带域名
Request::root();
// 获取URL访问根地址 包含域名
Request::root(true);
```

### 请求变量

1. Request 对象支持全局变量的检测、获取和安全过滤，支持$_GET、$_POST、...等

2. 为了方便演示，这里一律使用 Facade 的静态调用模式

3. 使用 has() 方法，可以检测全局变量是否已经设置

   ```php
   dump( Request::has('id','get'));
   dump( Request::has('username','post'));
   ```

4. Request 支持的所有变量类型方法

   | 方法       | 描述                           |
   | ---------- | ------------------------------ |
   | param      | 获取当前请求的变量             |
   | get        | 获取 $_GET 变量                |
   | post       | 获取 $_POST 变量               |
   | put        | 获取 PUT 变量                  |
   | delete     | 获取 DELETE 变量               |
   | session    | 获取 SESSION 变量              |
   | cookie     | 获取 $_COOKIE 变量             |
   | request    | 获取 $_REQUEST 变量            |
   | server     | 获取 $_SERVER 变量             |
   | env        | 获取 $_ENV 变量                |
   | route      | 获取 路由（包括PATHINFO） 变量 |
   | middleware | 获取 中间件赋值/传递的变量     |
   | file       | 获取 $_FILES 变量              |

5. `PARAM`类型变量是框架提供的用于自动识别当前请求的一种变量获取方式，是系统推荐的获取请求参数的方法，用法如下

   ```php
   // 获取当前请求的name变量
   Request::param('name');
   // 获取当前请求的所有变量（经过过滤）
   Request::param();
   // 获取当前请求未经过滤的所有变量
   Request::param(false);
   // 获取部分变量
   Request::param(['name', 'email']);
   ```


### 请求类型

1. 有时，我们需要判断 Request  的请求类型，比如 GET、POST 等

2. 可以使用 method () 方法来判断当前的请求类型，当然。还有很多专用的请求判断

   | **用途**            | 方法                 |
   | ------------------- | -------------------- |
   | 获取当前请求类型    | method               |
   | 判断是否GET请求     | isGet                |
   | 判断是否POST请求    | isPost               |
   | 判断是否PUT请求     | isPut                |
   | 判断是否DELETE请求  | isDelete             |
   | 判断是否AJAX请求    | isAjax               |
   | 判断是否PJAX请求    | isPjax               |
   | 判断是否为JSON请求  | isJson（`V5.1.38+`） |
   | 判断是否手机访问    | isMobile             |
   | 判断是否HEAD请求    | isHead               |
   | 判断是否PATCH请求   | isPatch              |
   | 判断是否OPTIONS请求 | isOptions            |
   | 判断是否为CLI执行   | isCli                |
   | 判断是否为CGI模式   | isCgi                |

3. 使用普通表单提交，通过 method() 方法获取类型

   ```php+HTML
   <form action="http://localhost/tp5.1/public/rely" method="post">
       <input type='text' name="name" value="lee">
       <input type="submit" value="提交">
   </form>
   ```

4. 在表单提交时，我们也可以设置请求类型伪装，设置隐藏字段 _method

5. 而在判断请求，使用 method(true) 可以获取原始请求，否则获取伪装请求

   ```php+HTML
   <input type="hidden" name="_method" value="PUT">
   Request::method(true);
   ```

6. 如果想更改请求伪装变量类型的名称，可以在 app.php 中修改

   ```php
   'var_method'		=>		'_method'
   ```

### HTTP头信息

1. 使用header() 方法可以输出 HTTP头信息，返回是数组类型，也可以单信息获取

   ```php
   dump(Request::header());
   dump(Request::header('host'));
   ```

### 请求缓存

1. 请求缓存仅对 GET 请求有效，并设置有效期

2. 可以设置全局请求缓存，在 app.php 中设置

   ```php
   'request_cache'		=>	true,
   'request_cache_expire'	=>	3600
   ```

3. 当第二次访问时，会自动获取请求缓存的数据响应输出，并发送 304 状态码

4. 如果要对路由设置一条缓存，直接使用 cache(3600) 方法

## 响应操作

1. 响应输出，之前已经都掌握了，包括 return、json()和view()

2. return默认会输出html 格式，配置文件默认设定的 default_return_type

3. 而背后 response 对象，可以用 response() 输出达到相同的效果

   ```php
   return response($data);
   ```

4. 使用 response() 方法可以设置第二参数，状态码，或调用 code() 方法

   ```php
   return response($data,201)
   return response($data)->code(202)
   ```

5. 使用 json()、view() 方法和response() 返回的数据类型不同，效果一样

   ```php
   return json($data,201);
   return json($data)->code(201);
   ```

6. 不但可以设置状态码，还可以设置header() 头文件信息

   ```php
   retuen json($data)->code(201)->header(['Cache-control'=>'on-cache,must-revalidate'])
   ```

### 重定向

1. 使用 redirect() 方法可以实现页面重定向，需要 return 执行

   ```php
   return redirect('https://xkbk.top/')
   ```

2. 站内重定向，直接输入路由地址或相对地址即可，需要用顶级域名，二级会报错

   ```php
   return redirect('/edit/5');
   ```

3. 也可以使用 params() 方法传递数组参数键值对的方式，进行跳转

   ```php
   return redirect('/edit/5')->params(['id'=>5]);
   return redirect('/edit/5',['id'=>5]);
   ```

### 文件下载

1. 文字下载和图片下载都使用 download() 方法即可，路径为实际路径

   ```php
   return \download('image.jpg','my');
   $data = '这是一个参数文本';
   return \download($data,'my',true);
   ```

## 验证器

### 验证器定义

1. 验证器的使用，我们必须先定义它，系统通提供了一条命令直接生成想要的类

   ```
   php think make:validate
   ```

2. 这条命令会自动在应用目录下 生成 common/validate 文件夹，并生成 User.php 类

   ```
   class User extends Validate
   ```

3. 自动生成了2个属性：$rule 表示定义规则、$message 表示错误提示信息

   ```php
   /**
        * 定义验证规则
        * 格式：'字段名'	=>	['规则1','规则2'...]
        *
        * @var array
        */	
   	protected $rule = [
   	    'name'      =>      'require|max:10|checkName:小柯', // 不能为空，不得大于10位，自定义规则
           'price'     =>      'number|between:1,100', // 必须是数值，1~100之间
           'email'     =>      'email' // 必须是邮箱格式
       ];
       
       /**
        * 定义错误信息
        * 格式：'字段名.规则名'	=>	'错误信息'
        *
        * @var array
        */	
       protected $message = [
           'name.require'      =>      '用户名不可以为空'
       ];
       // 自定义规则
       protected function checkName($value,$rele,$data,$field) {
           return $value != $rele ? true : '不可以使用这个昵称！';
       }
   ```

4. 如果不设置 $message 定义得话，将使用默认提示错误信息

5. 验证器定义好了，我们需要调用测试，创建一个Verify.php 控制器

   ```php+HTML
   <?php
   
   
   namespace app\test\controller;
   
   
   use app\common\validate\User;
   
   class Verfiy
   {
       public function index(){
           $data = [
               'name'      =>      '小柯',
               'price'     =>      '100',
               'email'     =>      '2998524835@qq.c'
           ];
           $validate = new User();
           // 判断验证是否通过
           if(!$validate->check($data)) {
                // $validate->getError() 获取到错误信息
                return dump($validate->getError());
           }
           return '验证通过！';
       }
   }
   ```

6. 控制器基类（需要继承 Controller）还提供了一个更加方便验证的方法，可以更容易的进行编码

   ```php
   $result = $this->validate([
      'name'      =>      '小柯',
      'price'     =>      '100',
      'email'     =>      '2998524835@qq.c'
   ],'\app\validate\User');
   if($result !== true) {
   	dump($result);
   }
   ```

7. 默认情况下，一旦有数据不符合规则，就立刻停止验证进行返回

8. 如果，我们要验证所有的错误信息，那需要手动开启批量验证

   ```php
   protected $batchValidate = true;
   ```

9. 默认情况下，验证失败后不会抛出异常，需要手动设置，如下

   ```php
   protected $failException = true;
   ```

10. 系统提供了常用的规则让开发者直接使用，我们也可以自定义规则

    ```php
     protected $rule = [
    	    'name'      =>      'require|max:10|checkName:小柯', // 不能为空，不得大于10位，自定义规则 不能出现 小柯
        ];
     // 自定义规则
        protected function checkName($value,$rele,$data,$field) {
          // 验证的数据
            dump($data);
            // 验证的字段名
            dump($field);
            return $value != $rele ? true : '不可以使用这个昵称！';
        }
    ```

11. 验证规则也是支持数组模式

    ```php
    	protected $rule = [
    	   'name'   => [
    	       'require',
               'max'    =>  10,
               'checkName'  =>  '小柯'
           ],
           'price'  => [
               'number',
               'between'    =>  '1,100',
           ],
           'email'  => [
               'email'
           ]
        ];
    ```

### 场景验证

1. 有时，我们并不希望所有的字段都得到验证，比如修改的时候，不验证邮箱

2. 可以在验证类 User.php 中，设置一个 $scene 属性，用来限定场景验证

   ```php
   protected  $scene = [
       	// 自定义
   	    'insert'        =>  ['name','price','email'],
           'edit'        =>  ['name','price']
       ];
   ```

3. 在控制器中使用 scene() 方法来限定场景

   ```php
   // scene 传递一个场景名称
   if(!$validate->scene('edit')->batch()->check($data)) {
                // $validate->getError() 获取到错误信息
                return dump($validate->getError());
   }
   ```

4. 在验证端，可以设置一个公共方法对场景的细节进行定义

   ```php
   public function sceneEdit()
   {
       $edit	= $this->only(['name','price']) // 仅对这2个字段进行验证
           		  ->remove('name','max')	// 移出掉对最大字段的限制
           		  ->append('price','require') // 增加一个不能为空的限制
        return $edit;
   }
   ```

5. 注意：请不要对一个字段进行2个或以上的移出和添加，会被覆盖

   ```php
   remove('name','xxx|yyy|zzz')或remove('name',['xxx','yyy','zzz']);
   而不是
   remove('name','xxx')->remove('name','yyy')->remove('name','zzz');
   ```

### 路由验证

1. 路由验证，即在路由的参数来调用验证类进行验证，和字段验证一模一样

   ```php
   protected $rule = [	
       'id'	=>	'number|between:1,10'
   ];
   ```

2. 使用验证类方法来验证 

   ```php
   // validate 第二个参数时 场景 可空
   Route::get('/verfiy','test/Verfiy/read')->validate(\app\common\validate\ValidateRoute::class);
   ```

3. 如果不用验证类，也可以使用独立验证方式，也可以使用对象化

   ```php
   Route::get('/verfiy','test/Verfiy/read')->validate([
   	'id'		=>		'number|betwenn:1,10'
   ],'edit',[
   	'id.number'	=> 'id必须是数值',
   ],true);
   ```

### 静态调用

1. 静态调用，即使用 facade 模型进行调用验证，非常适合单个数据的验证

2. 引入 facade 中的 Validate 时，和 think\Validate 冲突，只需要引入一个即可

   ```php
   // 验证邮箱是否合法
   dump(Validate::isEmail('bbbbb163@qq.com'));
   //验证值是否为空
   dump(Validate::isRequre('xioke'));
   //验证是否为数值
   dump(Validate::isNumber(15));
   ```

    

3. 调用返回的结果是 false 和 true 错误信息需要自行错误

4. 静态调用，也是支持多规则验证的，使用 checkRule() 方法实现

   ```php
   dump(Validate::eheckRule(10,'number|between:1,10'));
   ```

5. checkRule() 不支持错误信息，需要自己处理，但支持对象化规则定义

   ```php
   dump(Validate::eheckRule(10,ValidateRule::isNumber()->between('1,10')));
   ```

### 表单令牌

1. 表单令牌就是在表单中添加一个隐藏字段，随机生成一串字符，确定表示伪造

2. 这种随机产生的字符和服务器的 session 进行对比，通过则是合法的表单

3. 首先，创建一个带令牌的表单，在See控制器下的vali方法模板实现

   ```php+HTML
      <form action="/Test/See/from" method="post">
           <input type="text" name="name">
           <input type="hidden" name="__token__" value="{$Request.token}">
           <input type="submit">
       </form>
   ```

   

4. 为了验证系统内部的机制，可以通过打印测试内部的构造

   ```php
   // 打印出生成的 token 随机数
   echo Request::token();
   // 打印出保存到 session 的 token
   echo Session::get('_token_');
   ```

5. 验证部分，只需要使用内置规则 token 即可验证，具体流程如下

   ```php
    $data = [
        'name'		=>		input('post.name'),
        '__token__'	=>		input('post.__token__'),
    ];
    $validate = new Validate();
    $validate->rule([
        'name'  =>  'require|token'
    ]);
    if(!$validate->batch()->check($data)) {
        dump($validate->getError());
    }
   ```

### 内置规则

1. 内置的规则有很多，并且严格区分大小写，这里按照类别一一列出
2. 静态方法支持两种形式，比如::number() 或者是isNumber() 均可
3. 而 require 是 PHP 保留字，那么就必须用 isRequire() 或 must()

#### 格式验证

| 类名      | 说明                                     |
| --------- | ---------------------------------------- |
| require   | 不得为空 ::isRequire 或 must             |
| number    | 是否是纯数字，非负数小数                 |
| integer   | 是否是整数                               |
| float     | 是否是浮点数                             |
| boolean   | 是否是布尔值，或者 bool                  |
| email     | 是否是邮箱格式                           |
| array     | 是否是数组                               |
| accepted  | 是否是 yes、no、1 这三个值               |
| date      | 是否是有效日期                           |
| alpha     | 是否是有效纯字母                         |
| alphaNum  | 是否是字母和数字                         |
| alphaDash | 是否是字母和数字以及_-（下划线和破折号） |
| chs       | 是否是纯汉字                             |
| chsAlpha | 是否是汉字和字母 |
| chsAlphaNum | 是否汉字和字母和数字 |
| chsDash | 是否是汉字和字母和数字以及_-（下划线和破折号） |
| cntrl | 是否是控制字符（换行、缩进、空格） |
| graph | 是否是可打印字符（除空格） |
| print | 是否是可打印字符（含空格） |
| lower | 是否是小写字符 |
| upper | 是否是大写字符 |
| space | 是否空白字符 |
| xdigit | 是否是十六进制 |
| activeUrl | 是否是有效域名或IP地址 |
| url | 是否是有效URL地址 |
| ip | 是否是有效IP （支持 ipv4、ipv6） |
| dateFormat:Y-m-d | 是否是指定日期格式 Validate( '2021-5-1' , 'Y-m-d'  ) |
| mobile | 是否是有效手机 |
| idCard | 是否是有效身份证 |
| macAddr | 是否是有效 MAC 地址 |
| zip | 是否是有效邮编 |

#### 长度和区间验证类

| 类名                             | 说明                               |
| -------------------------------- | ---------------------------------- |
| in:1,2,3                         | 是否有某个值                       |
| notIn:1,2,3                      | 是否是没有某个值                   |
| beween:1,100                     | 是否是在区间之间                   |
| notBeween:1,100                  | 是否是不在区间之间                 |
| length:2,20                      | 是否字符长度在范围中               |
| length:4                         | 是否字符长度匹配                   |
| max:20                           | 是否字符最大长度                   |
| min:20                           | 是否字符最小长度                   |
| after:2020-1-1                   | 是否在指定日期之后                 |
| before:2020-1-1                  | 是否在指定日期之前                 |
| expire:2019-1-1,2020-1-1         | 是否当前操作是否在某个有效期内     |
| allowIp:221.221.78.1,192.168.0.1 | 验证当前请求的ip是否在某个范围之间 |
| denyIp:221.2221.78.1,192.168.0.1 | 验证当前请求的ip 是否被禁用        |

#### 字段比较类

| 类名               | 说明                         |
| ------------------ | ---------------------------- |
| confirm:password   | 是否和另一个字段匹配         |
| different:password | 是否和另一个字段不匹配       |
| eq:100             | 是否等于某个值，=、same 均可 |
| gt:100             | 是否大于某个值，支持 >       |
| egt:100            | 是否大于等于某个值，支持 <   |
| elt:100            | 是否小于等于某个值，支持 <=  |

#### 其它验证类

| 类名                | 说明                              |
| ------------------- | --------------------------------- |
| \d{6}               | 正则表达式验证                    |
| regex:\d{6}         | 正则表达式验证                    |
| file                | 判断是否是上传文件                |
| image:150,150,gif   | 判断图片（参数可选）              |
| fileExt:jpg,txt     | 判断文件允许的后缀                |
| fileMime:text/html  | 判断文件允许的文件类型            |
| fileSize:2048       | 判断文件允许的字节大小            |
| behavior:/app/...   | 判断行为验证                      |
| unique:user         | 判断字段的值是否存在 user 表中    |
| requireWith:account | 当account有值时，requireWith 必须 |

## Session

1. Session 第一次调用时，会按照 config/session.php 进行初始化

   ```php
   return [
       'id'             => '',
       // SESSION_ID的提交变量,解决flash上传跨域
       'var_session_id' => '',
       // SESSION 前缀
       'prefix'         => 'think',
       // 驱动方式 支持redis memcache memcached
       'type'           => '',
       // 是否自动开启 SESSION
       'auto_start'     => true,
   ];
   ```

2. 系统也支持通过方法调用的形式进行初始化，比如说 ::init() 方法

   ```php
   Session::init ([
       'id'             => '',
       // SESSION_ID的提交变量,解决flash上传跨域
       'var_session_id' => '',
       // SESSION 前缀
       'prefix'         => 'think',
       // 驱动方式 支持redis memcache memcached
       'type'           => '',
       // 是否自动开启 SESSION
       'auto_start'     => true,
   ]);
   ```

3. 所有配置 如下

   | 参数           | 描述                   |
   | -------------- | ---------------------- |
   | type           | session类型            |
   | expire         | session过期时间        |
   | prefix         | session前缀            |
   | auto_start     | 是否自动开启           |
   | use_trans_sid  | 是否使用 use_trans_sid |
   | var_session_id | 请求 session_id 变量名 |
   | id             | session_id             |
   | name           | session_name           |
   | path           | session 保存路径       |
   | domain         | session cookie_domain  |
   | use_cookies    | 是否使用 cookies       |
   | cache_limiter  | session_cache_limiter  |
   | cache_expire   | session_cache_expire   |
   | secure         | 安全选项               |
   | httponlt       | 使用httponly           |

4. 直接使用 ::set() 和 ::get() 方法去设置 Session 的存取

   ```php
   Session::set('name','xiaoke');
   echo Session::get('name');
   dump(Request::session());
   echo $_SESSION['think']['user']
   ```

5. ::set ()  赋值中，第三个参数可以设置前缀，即 Session 作用域

   ```php
   Session::set('name','xiaoke','tp');
   ```

6. ::get() 取值中，第二参数可以前调作用域，不写则默认当前作用域

   ```php
   Session::get('name','tp');
   ```

7. ::has() 判断当前作用域 session 是否赋值，第二参数可以指定作用域

   ```php
   Session::has('name');
   Session::has('name','tp');
   ```

8. ::delete 删除，默认删除当前作用域的值，第二参数可以指定作用域

   ```php
   Session::delete('name');
   Session::delete('name','tp');
   ```

9. ::prefix() 方法可以指定当前作用域

   ```php
   Session::prefix('tp')
   ```

10. ::pull 方法，取出当前的值，并删除这个 session ， 不存在返回null

    ```php
    echo Session::pull('name')
    ```

11. ::clear() 清除当前作用域的 session ，可以指定作用域

    ```php
    Session::clear('');
    Session::clear('tp')
    ```

12. 助手函数

    ```php
    // 赋值
    session('user','Mr.lee');
    // 带作用域赋值
    session('user','Mr.lee','tp');
    // 判断
    session('?user');
    // delete删除
    session('user',null);
    // 清理
    session(null);
    // 带作用域清理
    session(null,'tp');
    // 带作用域删除
    echo session('user','tp');
    // 删除
    echo session('user');
    ```

## Cookie

1. Cookie 是客户端存储，无须手动初始化，配置文件 cookie.php 中会自动初始化

   ```php
   return [
       // cookie 名称前缀
       'prefix'    => '',
       // cookie 保存时间
       'expire'    => 0,
       // cookie 保存路径
       'path'      => '/',
       // cookie 有效域名
       'domain'    => '',
       //  cookie 启用安全传输
       'secure'    => false,
       // httponly设置
       'httponly'  => '',
       // 是否使用 setcookie
       'setcookie' => true,
   ];
   ```

2. 当然你也可以自行初始化 和 Session 一样 也可以单独设置 前缀

   ```php
   Cookie::init([
       // cookie 名称前缀
       'prefix'    => '',
       // cookie 保存时间
       'expire'    => 0,
       // cookie 保存路径
       'path'      => '/',
       // cookie 有效域名
       'domain'    => '',
       //  cookie 启用安全传输
       'secure'    => false,
       // httponly设置
       'httponly'  => '',
       // 是否使用 setcookie
       'setcookie' => true,
   ]);
   Cookie.prefix('tp_');
   ```

3. ::set() 方法，创建一个最基本的 cookie，可以设置前缀、过期时间、数组

   ```php
      Cookie::set('user','Mr.lee');
      Cookie::set('user','Mr.lee',3600);
           Cookie::set('user','Mr.lee',[
               'prefix'    =>  'think_',
               'expire'    =>  3600
           ]);
   ```

   

4. ::forever() 方法，永久保存 Cookie( 就是十年的意思 )

   ```php
   Cookie::forever('user','Mr.lee');
   ```

5. ::has() 方法判断，判断是否赋值，第二参数可设置前缀

   ```php
   Cookie::has('user');
   Cookie::has('user','tp_');
   ```

6. ::get() 取值时，第二参数可以设置前缀

   ```php
   Cookie::get('user');
   Cookie::get('user','think_');
   ```

7. ::delete() 删除，第二参数可设置前缀

8. clear()，清空 Cookie，这里必须指定前缀，否则无法清空

9. 助手函数

   ```php
   // 初始化
   cookie([
       'prefix'	=>		'tp_',
       'expire'	=>		3600,	
   ]);
// 输出
   echo cookie('user');
// 设置
   cookie('user','Mr.lee','3600');
   // 删除
   cookie('user',null);
   // 清除
   cookie(null,'tp_');
   ```
   

## 分页功能

1. 不管是数据库操作还是模型操作，都使用 paginate() 方法来实现

   ```php
   // 每页显示5条
   $list = Db::name('user')->paginate(5);
   return json($list);
   ```

2. 通过生成的数据列表，可以得到分页得参数变量，具体如下

   total ( 总条数 )

   per_page ( 每页条数 )

   current_page ( 当前页码 )

   last_page ( 最终页码 ) 

3. 系统在模块上提供得方页功能 { $list|raw }

   ```php+HTML
   <table border="1" cellspacing="0">
       <thead>
       <th>ID</th>
       <th>名称</th>
       <th>年龄</th>
       <th>性别</th>
       <th>班级</th>
       </thead>
     <tbody>
     {volist  name='list' id='item'}
     <tr>
         <td  style="padding: 10px">{$item.id}</td>
         <td  style="padding: 10px">{$item.name}</td>
         <td  style="padding: 10px">{$item.age}</td>
         <td  style="padding: 10px">{$item.sex}</td>
         <td  style="padding: 10px">{$item.class}</td>
     </tr>
     {/volist}
     </tbody>
       <div>
           {$list|raw}
       </div>
   </table>
   
   ```

4. 也可以单独赋值方页得模板变量

   ```php
   $page = $list->render();
   $this->assing('page',$page);
   ```

5. 也可以单独获取到总记录数量

   ```php
   $total = $list->total();
   ```

6. 可以限定总记录数，比如，限定总记录只有10条

   ```php
   paginate(5,10)
   ```

7. 如果你使用模型方页，则可以通过获取器修改字段值，而方页本身也可以

   ```php
   each(function ($time,$key) {
   	$item['gender']		= '【'.$item['gender'].'】'
   	return $item;
   });
   ```

8. 也可以设置分页为简洁方页，就是没有 1，2，3这种，只有上下页

   ```php
   paginate(5,true)
   ```

## 上传功能

1. 前台

   ```php
   <form action="http://tp5.xkbk.top/Test/upload/" enctype="multipart/form-data" method="post">
     <input type="file" name="image" />
     <input type="submit" value="上传" />
   </form>
   ```

2. 后台

   ```php
   	// 上传单个文件
   	public function index()
       {
           // 获取上传的文件 image 表单 name 名
           $file = Request::file('image');
           // 移动到应用目录下的 uploads
           $info = $file->move('../application/uploads');
           // 判断上传信息
           if($info) {
               // 获取到后缀
               echo $info->getExtension();
               echo '<br>';
               // 获取带路径的地址
               echo $info->getSaveName();
               echo '<br>';
               // 文件名
               echo $info->getFilename();
           } else {
               // 上传失败获取错误信息
               echo $file->getError();
           }
   
       }
   ```

3. 如果是多个文件上传，只需要循环一下即可

   ```php+HTML
   <form action="http://tp5.xkbk.top/Test/upload/uploads" enctype="multipart/form-data" method="post">
       <input type="file" name="image" />
       <input type="file" name="image[]" />
       <input type="file" name="image[]" />
       <input type="submit" value="上传" />
   </form>
   ```

   ```php
    public function uploads(){
           // 获取上传的文件 image 表单 name 名
           $files = Request::file('image');
           foreach ($files as  $file) {
           // 移动到应用目录下的 uploads
           $info = $file->move('../application/uploads');
           // 判断上传信息
           if($info) {
               // 获取到后缀
               echo $info->getExtension();
               echo '<br>';
               // 获取带路径的地址
               echo $info->getSaveName();
               echo '<br>';
               // 文件名
               echo $info->getFilename();
           } else {
               // 上传失败获取错误信息
               echo $file->getError();
               }
           }
       }
   ```

4. 上传文件，可以通过 validate() 方法进行验证，包括大小限定、后缀 等等

   ```php
   $info = $file->validate([
   	'size'	=>	102400,
   	'ext'	=>	'jpg,gif,png',
   // 	type	=>	'text/html'
   ])
   ```

5. 默认情况下，上传的文件时以日期和微秒生成的方式，date

6. 生成的规则还支持另外两种方式：md5 和 sha1

   ```php
   $file->rule('md5')->move('../application/uploads')
   ```

7. 也可以通过传递一个方法或函数来自定义命名，比如 uniqid()

   ```php
   $file->rule('uniqid')  // uniqid() 产生一个微秒时间生成一个唯一的 ID
   ```

8. 在move() 方法的，第二参数设置为空字符串，可以表示按照原本的名称保存

   ```php
   $file->move('../application/uploads','')
   ```

9. 如果设置原本的名称生成，会被覆盖掉

10. 那么可以设置，在同名的基础上，不去覆盖

    ```php
    $file->move('../application/uploads',true,false)
    ```

## 缓存功能

1. 系统内置很多类型的缓存，除了 File 其他均需要结合相关产品

2. 我们这里主要演示 FIle 文本缓存

3. 在 app.php ,配置文件 cache.php 中进行缓存配置，可以 ::init() 

   ```php
   return [
       // 驱动方式
       'type'   => 'File',
       // 缓存保存目录
       'path'   => '',
       // 缓存前缀
       'prefix' => '',
       // 缓存有效期 0表示永久缓存
       'expire' => 0,
   ];
   ```

4. ::set() 方法，可以设置一个缓存，然后再 runtime/cache 查看生成结果

   ```php
   Cache::set('user','Mr.lee');
   ```

5. ::inc() 和 ::dec() 实现缓存数据的自增和自减操作

   ```php
   Cache::inc('num')
   Cache::inc('num',3)
   ```

6. ::has() 方法，判断缓存是否存在，返回布尔值

7. ::get() 方法，从缓存中获取到相应的数据，无数据返回null

8. ::delete() 方法，删除指定的缓存文件

   ```php
   Cache::delete('user');
   ```

9. ::pull () 方法，先获取缓存值，然后再删除掉这个缓存，无数据返回 null

10. ::clear() 方法，清空所有缓存

11. ::tag()，标签，可以将多个缓存归类到标签中，方便统一管理，比如清除

    ```php
    Cache::tag('tag')->set('user','Mr.lee');
    Cache::tag('tag')->set('age',20)
    Cache::tag('user')->set('name','xiaoke')
    Cache::tag('user')->set('name','xiaobai')
    Cache::tag('tag')->set(['name','xiaoqian'])
    ```

12. 助手函数的使用：cache ()

    ```php
    // 设置缓存
    cache('user','Mr.lee',3600);
    // 输出缓存
    eache('user');
    // 删除
    eache('user',null)
    ```

    