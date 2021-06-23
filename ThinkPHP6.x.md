# ThinkPHP6.x

安装

```
composer create-project topthink/think 文件夹名
```

## 模型

### 定义

1. 定义一个和数据库表匹配的模型

   ```
   class User extends Model
   ```
   
2. 模型会自动对应数据库表，并且有一套自己的命名规则

3. 模型类需要去除表前缀（tp_），采用驼峰命名，并且首字母大写

   ```
   tp_user			=>	User
   tp_user_typee	 =>	 UserType	
   ```

4. 如果你担心设置的模型名和 PHP 关键字冲突，可以开启应用类后缀

5. 比如设置：class User 这种，需要修改文件名，类名  UserModel

6. 然后在模型内设置 $name 属性为 UserModel 对应的表名即可

   ```
   protected $name =  'user';
   ```

### 设置模型

1. 设置表名称（用默认的就可以了）

   ````
   protected $name =  'user';
   ````

2. 你也可以设置其他的表

   ```
   protected $table =  'role';
   ```

3. 设置主键，默认主键为 id，你可以设置其他主键

   ```
   protected $pk =  'uid';
   ```

4. 初始化操作

   ```php
   protected static function init()
   {
       parent::init();
   	// 第一次实例化的时候 执行 init
   	echo '初始化';
   }
   ```


### 数据新增

1. 使用实例化的方式添加一条数据，首先实例化方式如下，两种均可

   ```php
   $user = new UserModel();
   $user = new \app\model\User();
   ```

2. 设置要新增的数据，然后用 save()方法写入到数据库中，save()返回布尔值； 

      ```php
      $user->username = '李白';
      $user->password = '123';
      $user->gender = '男'; 
      $user->email = 'libai@163.com';
      $user->price = 100; 
      $user->details = '123';
      $user->uid = 1011; 
      $user->save();
      ```

3. 也可以通过 save()传递数据数组的方式，来新增数据； 

      ```php
      $user = new UserModel();
      $user->save([
      	'username' => '李白', 
      	'password' => '123', 
      	'gender'   => '男', 
      	'email'    => 'libai@163.com', 
      	'price'    => 100, 
      	'details'  => '123', 
      	'uid'      => 1011 
      ]);
      ```

4.  使用 allowField()方法，允许要写入的字段，其它字段就无法写入了； 

      ```php
      $user->allowField(['username','email', 'password','details'])->save(...)
      ```

5. 模型新增也提供了 replace()方法来实现 REPLACE into 新增； 

      ```php
      $user->replace()->save();
      ```

6. 当新增成功后，使用$user->id，可以获得自增 ID（主键需是 id）； 

      ```php
      echo $user->id;
      ```

7. 使用 saveAll()方法，可以批量新增数据，返回批量新增的数组； 

      ```php
      $dataAll = [ 
          [ 'username' => '李白1',
           'password'  => '123', 
           'gender'    => '男', 
           'email'     => 'libai@163.com', 
           'price'     => 100,
           'details'   => '123', 
           'uid'       => 1011 
          ], [ 
              'username' => '李白2',
              'password' => '123',
              'gender'   => '男',
              'email'    => 'libai@163.com',
              'price'    => 100, 
              'details'  => '123', 
              'uid'      => 1011 
          ] ];
      $user = new UserModel(); 
      dump($user->saveAll($dataAll));
      ```

8. 使用::create()静态方法，来创建要新增的数据； 

      ```php
      $user = UserModel::create([ 
          'username' => '李白',
          'password' => '123',
          'gender'   => '男',
          'email'    => 'libai@163.com',
          'price'    => 100,
          'details'  => '123',
          'uid'      => 1011 
      ], ['username', 'password', 'details'], false); 
      //参数 1 是新增数据数组，必选 //参数 2 是允许写入的字段，可选 //参数 3 为是否 replace 写入，默认 false 为 Insert 写入
      ```


### 数据删除

1.  使用 find()方法，通过主键(id)查询到想要删除的数据； 然后再通过 delete()方法，将数据删除，返回布尔值； 

   ```
   $user = UserModel::find(93);
   $user->delete();
   ```

2.  也可以使用静态方法调用 destroy()方法，通过主键(id)删除数据； 

   ```
   UserModel::destroy(92)
   ```

3. 静态方法 destroy()方法，也可以批量删除数据

   ```
   UserModel::destroy([80, 90, 91]);
   ```

4. 通过数据库类的查询条件删除； 

   ```
   UserModel::where('id', '>', 80)->delete();
   ```

5. 使用闭包的方式进行删除； 

   ```php
   UserModel::destroy(function ($query) { 
   	$query->where('id', '>', 80); 
   });
   ```

### 数据更新

1.  使用 find()方法获取数据，然后通过 save()方法保存修改，返回布尔值； 

   ```php
   $user = UserModel::find(118);
   $user->username = '李黑';
   $user->email = 'lihei@163.com';
   $user->save();
   ```

2.  通过 where()方法结合 find()方法的查询条件获取的数据，进行修改； 

   ```php
   $user = UserModel::where('username', '李黑')->find();
   $user->username = '李白';
   $user->email = 'libai@163.com'; 
   $user->save();
   ```

3.  save()方法只会更新变化的数据，如果提交的修改数据没有变化，则不更新

4. 但如果你想强制更新数据，即使数据一样，那么可以使用 force()方法； 

   ```php
   $user->force()->save();
   ```

5.  Db::raw()执行 SQL 函数的方式，同样在这里有效； 

   ```php
   $user->price = Db::raw('price+1');
   ```

6.  使用 allowField()方法，允许要更新的字段，其它字段就无法写入了； 

   ```php
   $user->allowField(['username','email'])->save(...)
   ```

7. 通过 saveAll()方法，可以批量修改数据，返回被修改的数据集合； 

   ```php
   $list = [ 
   [ 'id'		=> 118, 
   'username'	=> '李白',
   'email'		=> 'libai@163.com'
   ], [
   'id'		=> 128,
   'username'	=> '李白',
   'email'		=> 'libai@163.com'
   ], [
   'id'	    => 129, 
   'username'	=> '李白', 
   'email'		=> 'libai@163.com
   ']]; 
   $user->saveAll($list);
   
   ```

8. 批量更新 saveAll()只能通过主键 id 进行更新

9. 使用静态方法::update()更新，返回的是对象实例； 

   ```php
   UserModel::update([ 
   'id' 		=> 118, 
   'username' 	=> '李黑',
   'email' 	=> 'lihei@163.com'
   ]);
   UserModel::update([ 
   'username' => '李黑',
   'email'    => 'lihei@163.com' 
   ],['id'=>118]);
   UserModel::update([ 
   'username' 	=> '李黑',
   'email' 	=> 'lihei@163.com'
   ], ['id'=>118], ['username']); //只更新 username
   ```

10. 模型的新增和修改都是 save()进行执行的，它采用了自动识别体系来完成

11.  实例化模型后调用 save()方法表示新增，查询数据后调用 save()表示修改； 

12.  当然，如果在 save()传入更新修改条件后也表示修改；

### 数据查询

1. 使用 find()方法，通过主键(id)查询到想要的数据； 

   ```php
   $user = UserModel::find(129); 
   return json($user);
   ```

2. 也可以使用 where()方法进行条件筛选查询数据；

   ```php
   $user = UserModel::where('username', '辉夜')->find(); 
   return json($user);
   ```

3. 调用 find()方法时，如果数据不存在则返回 Null

4.  同上，还有 findOrEmpty()方法，数据不存在返回空模型；

5.  此时，可以后使用 isEmpty()方法来判断，是否为空模型； 

   ```php
   $user = UserModel::findOrEmpty(1111); 
   if ($user->isEmpty()) 
   {
   echo '空模型，无数据！'; 
   }
   ```

6.  使用 select([])方式，查询多条指定 id 的字段，不指定就是所有字段； 

   ```php
   $user = UserModel::select([19,20,21]); 
   foreach ($user as $key=>$obj) 
   { 
   echo $obj->username;
   }
   ```

7. 模型方法也可以使用 where 等连缀查询，和数据库查询方式一样； 

   ```php
   $user = UserModel::where('status', 1)->limit(5)->order('id', 'desc')->select();
   ```

8. 获取某个字段 value()或者某个列 column()的值； 

   ```php
   UserModel::where('id', 79)->value('username');
   UserModel::whereIn('id',[79,118,128])->column('username','id');
   ```

9. 模型支持动态查询：getBy*，*表示字段名； 

   ```php
   UserModel::getByUsername('辉夜'); 
   UserModel::getByEmail('huiye@163.com');
   ```

10. 模型支持聚合查询：max、min、sum、count、avg 等；

    ```php
    UserModel::max('price');
    ```

11.  使用 chunk()方法可以分批处理数据，数据库查询时讲过，防止一次性开销过大； 

    ```php
    UserModel::chunk(5, function ($users) { 
     foreach($users as $user) 
    { 
         echo $user->username; 
     } 
    echo '<br>------<br>'; 
    });
    ```

12. 可以利用游标查询功能，可以大幅度减少海量数据的内存开销，它利用了 PHP 生 成器特性。每次查询只读一行，然后再读取时，自动定位到下一行继续读取

    ```php
    foreach (UserModel::where('status', 1)->cursor() as $user) 
    {
    echo $user->username; 
    echo '<br>------<br>'; 
    }
    ```

### 字段设置

1. 模型的数据字段和表字段是对应关系，默认会自动获取， 包括字段的类型

2. 自动获取会导致增加一次查询，如果在模型中配置字段信息，会减少内存的开销

3. 可以在模型设置 $schema 字段，明确定义字段信息，字段需要对应表完整

   ```php
   // 设置字段信息，需要完整的数据表字段    
   protected $scene = [
           'id'            =>  'int',
           'username'      =>  'string',
           'status'        =>  'int',
           'create_time'   =>  'datetime',
           '....'          =>  '...'
   ];
   ```

4. 系统提供了一条命令，生成一个字段信息缓存，可以自动生成

   ```
   php think optimize:schema
   ```

5.  生成后的字段缓存文件在 runtime 下 schema 文件加下； 

6.  我们可以先把这里的键值对复制到$schema 属性上，开启 trace 测试效果； 

7.  这时，在控制器执行查询，会发现减少了一次 SQL 查询；

8.  只不过，大可不必设置$schema 属性，因为它只对模型有效

9.  如果想模型和数据库 Db 类同时有效，直接运用字段缓存文件即可；

10.  默认情况下字段缓存文件是关闭状态，需要在 config/database.php 开启； 

    ```php
    // 开启字段缓存 
    'fields_cache' => true,
    ```

11.  当数据获取到后，想要单独获取数据可以用->和数组方式来获取； 

    ```php
    $user = UserModel::find(19); 
    echo $user->username; 
    echo $user['email'];
    ```

12. 如果我们在模型端把数据整理好，交给控制器直接调用，如下方式： 

    ```php
    //模型端 
    public function getUsername($id) 
    { 
    $obj = $this->find($id);
    return $obj->getAttr('username');
    }
    
    //控制器端调用 
    $user = new UserModel();
    return $user->getUsername(19);
    
    ```

13. 字段的赋值操作，也可以是->和数组方式，作用就是提交给模型处理； 

    ```php
    $user = new UserModel(); 
    $user->username = 'Mr.Lee'; 
    $user['email'] = 'lee@163.com';
    ```

14.  默认情况下，字段是严格区分大小写的，也就是需要和数据表字段保持一致； 

    ```
    echo $user->create_time;
    ```

15. 我们可以在模型属性$strict 设置为 false 即可实现非严格字段； 

    ```php
    echo $user->createTime; //并非肆无忌惮的不严格，只能首字母大写
    ```

### 模型获取器

1. 获取器的作用是对模型实例的数据做出自动处理

2. 一个获取器对应模型的一个特殊方法，该方法为 public

3. 方法名的命名规范：getFieldAttr()

4. 举个例子，数据库表示状态 status 字段采用的是数值

5. 而页面上，我们需要输出 status 字段希望是 中文，就可以使用获取器

6. 在 User 模型端，我们创建一个对外的方法然下

   ```php
   public function getStatusAttr($value)
   {
           $status = [0=>'禁用',1=>'启用'];
           return $status[$value];
   }
   ```

7. 然后，在控制器端，直接输出数据库字段的值即可得到获取器转换的对应值

   ```php
   return json(UserModel::select());
   ```

8. 除了 getFieldAttr 中 Field 可以是字段值，也可以是自定义的虚拟字段； 

   ```php
   public function getNothingAttr($value, $data) 
   { 
   	$myGet = [-1=>'删除', 0=>'禁用', 1=>'正常', 2=>'待审核']; 
   	return $myGet[$data['status']]; 
   }
   return $user->nothing
   ```

9.  Nothing 这个字段不存在，而此时参数$value 只是为了占位，并未使用； 

10. 第二个参数$data 得到的是筛选到的数据，然后得到最终值

11. 如果你定义了获取器，并且想获取原始值，可以使用 getData()方法； 

    ```
    return $user->getData('status');
    ```

12. 直接输出无参数的 getData()，可以得到原始值，而$user 输出是改变后的； 

    ```
    dump($user->getData());
    dump($user);
    ```

13. 使用 WithAttr 在控制器端实现动态获取器，比如设置所有 email 为大写； 

    ```php
    $user = UserModel::WithAttr('email', function ($value) { 
    return strtoupper($value); 
    })->select();
    return json($user);
    ```

14.  使用 WithAttr 在控制器端实现动态获取器，比如设置 status 翻译为中文； 

    ```php
    $user = UserModel::WithAttr('status', function ($value) { 
    $status = [-1=>'删除', 0=>'禁用', 1=>'正常', 2=>'待审核']; 
    return $status[$value]; 
    })->select();
    return json($user);
    ```

15.  同时定义了模型获取器和动态获取器，那么动态获取器优先级更高；

### 模型修改器

1. 模型修改器的作用，就是对模型设置对象的值进行处理； 

2. 比如，我们要新增数据的时候，对数据就行格式化、过滤、转换等处理

3.  模型修改器的命名规则为：setFieldAttr； 

4.  我们要设置一个新增，规定邮箱的英文都必须大写，修改器如下： 

   ```php
   public function setEmailAttr($value)
   { 
   	return strtoupper($value); 
   }
   ```

5. 除了新增，会调用修改器，修改更新也会触发修改器； 

6. 模型修改器只对模型方法有效，调用数据库的方法是无效的，比如->insert();

### 模型查询范围 

1. 在模型端创建一个封装的查询或写入方法，方便控制器端等调用； 

2.  比如，封装一个筛选所有性别为男的查询，并且只显示部分字段 5 条； 

3. 方法名规范：前缀 scope，后缀随意，调用时直接把后缀作为参数使用； 

   ```php
   public function scopeMale($query) 
   { 
   	$query->where('gender', '男')->field('id,username,gender,email')->limit(5); 
   }
   ```

4.  在控制器端，我们我们直接调用并输出结果即可； 

   ```php
   public function scope() 
   { 
   	$result = UserModel::scope('male')->select(); 
   	//$result = UserModel::male()->select(); 
   	return json($result); 
   }
   ```

5. 查询封装可以传递参数，比如，通过邮箱查找某人； 

   ```php
   //模型端
   public function scopeEmail($query, $value) 
   { 
   	$query->where('email', 'like', '%'.$value.'%'); 
   }
   // 控制端
   $result = UserModel::scope('email', 'xiao')->select(); 
   //$result = UserModel::email('xiao')->select(); return json($result)
   ```

6.  也可以实现多个查询封装方法连缀调用，比如找出邮箱 xiao 并大于 80 分的； 

   ```php
   public function scopePrice($query, $value) 
   { 
   	$query->where('price', '>', $value); 
   }
   // 控制端
   $result = UserModel::scope('email', 'xiao')->scope('price', 80)->select();
   //$result = UserModel::email('xiao') // ->price(80) // ->select(); return json($result);
   ```

7.  查询范围只能使用 find()和 select()两种方法； 

8.  全局范围查询，就是在此模型下不管怎么查询都会加上全局条件； 

   ```php
   // 定义全局的查询范围 
   protected $globalScope = ['status'];
   //全局范围 
   public function scopeStatus($query) 
   { 
       $query->where('status',1); 
   }
   ```

9. 在定义了全局查询后，如果想取消这个查询的所有全局查询，可以用下面方法； 

   ```
   UserModel::withoutGlobalScope()
   ```

10. 在定义了全局查询后，如果想取消这个查询的部分全局查询，可以添加参数指定； 

    ```
    UserModel::withoutGlobalScope(['status'])
    ```

### 模型搜索器 

1.  搜索器是用于封装字段（或搜索标识）的查询表达式，类似查询范围； 

2. 一个搜索器对应模型的一个特殊方法，该方法为 public； 

3. 方法名的命名规范为：searchFieldAttr()； 

4.  举个例子，我们要封装一个邮箱字符模糊查询，然后封装一个时间限定查询

5. 在 User 模型端，我创建两个对外的方法，如下： 

   ```php
   public function searchEmailAttr($query, $value, $data) 
   { 
   	$query->where('email', 'like', $value.'%'); 
   }
   public function searchCreateTimeAttr($query, $value, $data) 
   { 
   	$query->whereBetweenTime('create_time', $value[0], $value[1]); 
   }
   ```

6. 然后，在控制器端，通过 withSearch()方法实现模型搜索器的调用； 

   ```php
   $result = UserModel::withSearch(['email', 'create_time'],[
   'email' 	  =>  'xiao',
   'create_time'  =>  ['2014-1-1', '2017-1-1'] 
   ])->select();
   ```

7. withSearch()中第一个数组参数，限定搜索器的字段，第二个则是表达式值； 

8. 如果想在搜索器查询的基础上再增加查询条件，直接使用链式查询即可； 

   ```
   UserModel::withSearch(...)->where('gender', '女')->select()
   ```

9. 如果你想在搜索器添加一个可以排序的功能，具体如下： 

   ```php
   public function searchEmailAttr($query, $value, $data) 
   {
   $query->where('email', 'like', $value.'%');
   if (isset($data['sort'])){ 
   	$query->order($data['sort']); 
   	} 
   }
   ```

   ```php
   $result = UserModel::withSearch(['email', 'create_time'],[
   'email' 	  => 'xiao', 
   'create_time' => ['2014-1-1', '2017-1-1'],
   'sort' 		  => ['price'=>'desc'] 
   ])->select();
   ```

10.  搜索器的第三个参数$data，可以得到 withSearch()方法第二参数的值； 

11. 字段也可以设置别名：'create_time'=>'ctime'

### 模型数据集 

1.  数据集也是直接继承 collection 类，所以和数据库方式一样； 

2. 数据集对象和数组操作方法一样，循环遍历、删除元素等

3. 判断数据集是否为空，我们需要采用 isEmpty()方法； 

   ```php
   $resut = UserModel::where('id', 111)->select(); 
   if ($resut->isEmpty()) { 
   	return '没有数据！'; 
   }
   ```

4.  更多数据集方法，直接参考数据库那篇的表格即可； 5. 使用模型方法 hidden()可以隐藏某个字段，使用 visible

5. 使用模型方法 hidden()可以隐藏某个字段，使用 visible()显示只某个字段； 

6. 使用 append()可以添加某个获取器字段，使用 withAttr()对字段进行函数处理； 

   ```php
   $result = UserModel::select(); 
   $result->hidden(['password'])->append(['nothing'])->withAttr('email', function ($value) { 
   	return strtoupper($value); 
    }); 
   return json($result);
   ```

### 自动时间戳和只读字段

1. 如果你想全局开启，在 database.php 中，设置为 true

   ```
   // 自动写入时间戳字段
   'auto_timestamp' => true,
   ```

2. 如果你只想设置某一个模型开启，需要设置特有字段； 

   ```
   //开启自动时间戳 
   protected $autoWriteTimestamp = true;
   ```

3. 当然，还有一种方法，就是全局开启，单独关闭某个或某几个模型为 false； 

4.  自动时间戳开启后，会自动写入 create_time 和 update_time 两个字段； 

5.  此时，它们的默认的类型是 int，如果是时间类型，可以更改如下： 

   ```php
   'auto_timestamp' => 'datetime', //或 
   protected $autoWriteTimestamp = 'datetime';
   ```

6.  都配置完毕后，当我们新增一条数据时，无须新增 create_time 会自动写入时间； 

7. 同理，当我们修改一条数据时，无须修改 update_time 会自动更新时间

8. 自动时间戳只能在模型下有效，数据库方法不可以使用； 

9. 如果创建和修改时间戳不是默认定义的，也可以自定义； 

   ```php
   protected $createTime = 'create_at'; 
   protected $updateTime = 'update_at';
   ```

10. 如果业务中只需要 create_time 而不需要 update_time，可以关闭它； 

    ```php
    protected $updateTime = false;
    ```

11. 也可以动态实现不修改 update_time，具体如下： 

    ```
    $user->isAutoWriteTimestamp(false)->save();
    ```

#### 模型只读字段

1.  模型中可以设置只读字段，就是无法被修改的字段设置； 

2. 我们要设置 username 和 email 不允许被修改，如下：

   ```
   protected $readonly = ['username', 'email'];
   ```

3.  除了在模型端设置，也可以动态设置只读字段；

   ```
   $user->readonly(['username', 'email'])->save();
   ```

4. 同样，只读字段只支持模型方式不支持数据库方式；

###  模型的数据类型和转换

1. 系统可以通过模型端设置写入或读取时对字段类型进行转换； 

2. 我们这里，通过读取的方式来演示部分效果；

3. 在模型端设置你想要类型转换的字段属性，属性值为数组； 

   ```
   protected $type = [ 
   'price' 		=> 'integer',
   'status' 		=> 'boolean',
   'create_time' 	 => 'datetime:Y-m-d' 
   ];
   ```

4.  数据库查询读取的字段很多都是字符串类型，我们可以转换成如下类型： 

   integer(整型)、float(浮点型)、boolean(布尔型)、array(数组) object(对象)、serialize(序列化)、json(json)、timestamp(时间戳) datetime(日期)

5.  由于数据库没有那么多类型演示，常用度不显著，我们提供几个方便演示的； 

   ```php
   public function typeC() 
   { 
       $user = UserModel::find(19); var_dump($user->price); 
       var_dump($user->status); 
       var_dump($user->create_time); 
   }
   ```

6.  类型转换还是会调用属性里的获取器等操作，编码时要注意这方面的问题； 

7. 废弃字段，当某个字段在开发项目版本升级中不再使用，可以设置为废弃字段； 

8. 设置废弃字段后，这个字段就不在查询数据列表里了，写入忽略(存疑)；

   ```
   //设置废弃字段
   protected $disuse = ['status', 'uid'];
   ```

### JSON 字段

#### 数据库 JSON 

1. 数据库写入 JSON 字段，直接通过数组的方式即可完成； 

   ```php
   $data = [ 
   'username'  => '辉夜',
   'password' 	=> '123',
   'gender' 	=> '女',
   'email' 	=> 'huiye@163.com',
   'price' 	=> 90,
   'details' 	=> '123',
   'uid' 		=> 1011,
   'status' 	=> 1,
   'list' => ['username'=>'辉夜', 'gender'=>'女', 'email'=>'huiye@163.com'], ];
   Db::name('user')->json(['list'])->insert($data);
   ```

2. 如果要查询数据时，正确转换 json 数据格式，也需要设置 json 方法； 

   ```php
   Db::name('user')->json(['list'])->find(278);
   ```

3. 如果要将 json 字段里的数据作为查询条件，可以通过如下方式实现： 

   ```php
   $user = Db::name('user')->json(['list')-> where('list->username', '辉夜')->find();
   ```

4. 如果想完全修改 json 数据，可以使用如下的方式实现：

   ```php
   $data['list'] = [
   'username'	=> '李白',
   'gender'	=> '男',
   'email'		=> 'libai@163.com'
   ]; Db::name('user')->json(['list']) ->where('id', 278)->update($data);
   ```

5. 如果只想修改 json 数据里的某一个项目，可以使用如下的方式实现： 

   ```php
   $data['list->username'] = '李黑'; Db::name('user')->json(['list']) ->where('id', 278)->update($data);
   ```

#### 模型 JSON 

1.  想要写入 json 字段的字符字段，需要设置； 

   ```
   protected $json = ['list'];
   ```

2. 使用模型方式去新增包含 json 数据的字段；

   ```php
   $user = new UserModel(); 
   $user->username = '李白'; 
   $user->password = '123';
   $user->gender = '男';
   $user->email = 'libai@163.com';
   $user->price = 100;
   $user->uid = 1011; 
   $user->status = 1; 
   $user->details = 123; 
   $user->list = ['username'=>'辉夜', 'gender'=>'女 ','email'=>'huiye@163.com']; 
   $user->save();
   ```

3.  也可以通过对象的方式，进行对 json 字段的写入操作； 

   ```php
   $list = new \StdClass();
   $list->username = '辉夜';
   $list->gender = '女'; 
   $list->email = 'huiye@163.com';
   $list->uid = 1011;
   $user->list = $list;
   ```

4. 通过对象调用方式，直接获取 json 里面的数据； 

   ```php
   $user = UserModel::find(278);
   return $user->list->username;
   ```

5.  通过 json 的数据查询，获取一条数据

   ```php
   $user = UserModel::where('list->username', '辉夜')->find();
   return $user->list->email;
   ```

6. 更新修改 json 数据，直接通过对象方式即可； 

   ```php
   $user = UserModel::find(278); 
   $user->list->username = '李白';
   $user->save();
   ```

### 模型的软删除

1. 介于数据库软删除没有太多的可操作的方法，官方手册推荐使用模型软操作； 

2. 首先，需要在模型端设置软删除的功能，引入 SoftDelete，它是 trait

   ```php
   use SoftDelete; 
   protected $deleteTime = 'delete_time';
   ```

3. delete_time 默认我们设置的是 null，如果你想更改这个默认值，可以设置： 

   ```php
   protected $defaultSoftDelete = 0;
   ```

4. 软删除和方法如下，包括 destroy()和 delete()： 

   ```php
   UserModel::destroy(289); 
   UserModel::find(287)->delete();
   ```

5.  默认情况下，开启了软删除功能的查询，模型会自动屏蔽被软删除的数据； 

   ```php
   $user = UserModel::select(); 
   return json($user);
   ```

6. 在开启软删除功能的前提下，使用 withTrashed()方法取消屏蔽软删除的数据； 

   ```php
   $user = UserModel::withTrashed()->select();
   return json($user);
   ```

7. 如果只想查询被软删除的数据，使用 onlyTrashed()方法即可； 

   ```php
   $user = UserModel::onlyTrashed()->select(); 
   return json($user);
   ```

8.  如果想让某一条被软删除的数据恢复到正常数据，可以使用 restore()方法； 

   ```php
   $user = UserModel::onlyTrashed()->find(); 
   $user->restore();
   ```

9. 如果想让一条软删除的数据真正删除，在恢复正常后，使用 force()和 delete()； 

   ```php
   $user = UserModel::onlyTrashed()->find(193); 
   $user->restore(); $user->force()->delete(); 
   //或 UserModel::destroy(288, true)
   ```

### 模型和数据库的事件

#### 数据库事件 

1. 当你执行增删改查的时候，可以触发一些事件来执行额外的操作； 

2.  这些额外的操作事件，可以部署在构造方法里等待激活执行； 

3.  数据库事件方法为 Db::event('事件名', '执行函数')，具体事件名如下：

   | before_select | SELECT 查询前回调     |
   | ------------- | --------------------- |
   | before_find   | FIND 前回调           |
   | after_insert  | INSERT 操作成功后回调 |
   | after_update  | UPDATE 操作成功后回调 |
   | after_delete  | DELETE 操作成功后回调 |

4.  数据库事件只支持：find、select、update、delete、insert 这几个方法； 

5. 在控制器端，事件一般可以写在初始化方法里，方便统一管理； 

   ```php
   public function initialize() 
   { 
   	Db::event('before_select', function ($query) {
   		echo '执行了批量查询操作！'; 
   	});
   	Db::event('after_update', function ($query) {
       	echo '执行了修改操作！'; 
       });
   }
   ```

#### 模型事件

| **事件** | 描述 | 事件方法名 |
| -------- | ---- | ---- |
| after_read | 查询后 |onAfterRead|
| before_insert | 新增前 |onBeforeInsert|
| after_insert | 新增后 |onAfterInsert|
| before_update | 更新前 |onBeforeUpdate|
| after_update | 更新后 |onAfterUpdate|
| before_write | 写入前 |onBeforeWrite|
| after_write | 写入后 |onAfterWrite|
| before_delete | 删除前 |onBeforeDelete|
| after_delete | 删除后 |onAfterDelete|
| before_restore | 恢复前 |onBeforeRestore|
| after_restore | 恢复后 |onAfterRestore|

1. 在模型端使用静态方法调用即可完成事件触发； 

   ```php
   protected static function onAfterRead($query) 
   { 
   	echo '执行了查询方法'; 
   }
   protected static function onBeforeUpdate($query) 
   { 
   	echo '准备修改中...';
   }
   protected static function onAfterUpdate($query) 
   { 
   	echo '修改完毕...'; 
   }
   ```

### 模型关联

1. 关联模型，顾名思义，就是将表与表之间进行关联和对象化，更高效的操作数据； 

2. 我们已经有了一张 tp_user 表，主键为：id；我们需要一个附属表，来进行关联； 

3.  附属表：tp_profile，建立两个字段：user_id 和 hobby，外键是 user_id

   ![image-20210623095253189](C:\Users\xiaoke\AppData\Roaming\Typora\typora-user-images\image-20210623095253189.png)

4. 创建 User 模型和 Profile 模型，均为空模型； 

5.  User 模型端，需要关联 Profile，具体方式如下： 

   ```php
   class User extends Model 
   {
    public function profile() 
   { 	
       //hasOne表示一对一关联，参数一表示附表，参数二外键，默认user_id 
   	return $this->hasOne(Profile::class,'user_id'); 
    } 
   }
   ```

6. 创建一个控制器用于测试输出：Grade.php； 

   ```
   $user = UserModel::find(21); 
   return json($user->profile); 
   return $user->profile->hobby
   ```

7.  对于关联方式，系统提供了 9 种方案，具体如下

   | **模型方法**   | **关联类型** |
   | -------------- | ------------ |
   | hasOne         | 一对一       |
   | belongsTo      | 一对一       |
   | hasMany        | 一对多       |
   | hasOneThrough  | 远程一对一   |
   | hasManyThrough | 远程一对多   |
   | belongsToMany  | 多对多       |
   | morphMany      | 多态一对多   |
   | morphOne       | 多态一对一   |
   | morphTo        | 多态         |


#### 一对一关联

##### hasOne 

1. hasOne 模式，适合主表关联附表，具体设置方式如下

   ```php
   // hasOne('关联模型',['外键','主键']); 
   return $this->hasOne(Profile::class,'user_id', 'id');
   ```

   关联模型（必须）：关联的模型名或者类名 外键：默认的外键规则是当前模型名（不含命名空间，下同）+_id ，例如 user_id 主键：当前模型主键，默认会自动获取也可以指定传入

2. 使用 save()方法，可以设置关联修改，通过主表修改附表字段的值； 

   ```php
   $user = UserModel::find(19); 
   $user->hobby->save(['content'=>'酷爱小姐姐']);
   ```

3.  ->hobby属性方式可以修改数据，->hobby()方法方式可以新增数据； （ hobby 是 模型端 关联的方法名可自定义 ）

   ```php
   $user->profile()->save(['hobby'=>'不喜欢吃青椒']);
   ```

##### belongsTo 

1. belongsTo 模式，适合附表关联主表，具体设置方式如下: 

   ```php
   //belongsTo('关联模型',['外键','关联主键']);
   return $this->belongsTo(Profile::class,'user_id', 'id');
   ```

   关联模型（必须）：模型名或者模型类名 外键：当前模型外键，默认的外键名规则是关联模型名+_id 关联主键：关联模型主键，一般会自动获取也可以指定传入

2.  对于 belongsTo()的查询方案

   ```
   $profile = ProfileModel::find(1);
   return $profile->user->email;
   ```

3.  使用 hasOne()也能模拟 belongsTo()来进行查询； 

   ```php
   //参数一表示的是User 模型类的profile 方法，而非Profile 模型类 
   $user = UserModel::hasWhere('profile', ['id'=>2])->find(); 
   return json($user);
   //采用闭包，这里是两张表操作，会导致id识别模糊，需要指明表 
   $user = UserModel::hasWhere('profile', function ($query) { 
   	$query->where('profile.id', 2); 
   })->select();
   return json($user);
   ```

#### 一对多关联

1.  hasMany 模式，适合主表关联附表，实现一对多查询，具体设置方式如下: 

   ```
   hasMany('关联模型',['外键','主键']); 
   return $this->hasMany(Profile::class,'user_id', 'id');
   ```

   关联模型（必须）：模型名或者模型类名 外键：关联模型外键，默认的外键名规则是当前模型名+_id 主键：当前模型主键，一般会自动获取也可以指定传入

   