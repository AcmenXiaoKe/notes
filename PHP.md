# PHP

- 每一段代码结束必须加 ;
- 可以配合html来使用

## 基础语法

### 输出

```PHP
<?php
echo '输出的内容';
?>
```

### 定义变量

```php
<?php
// 定义变量 $名称 它是一个局部变量 如果其他地方需要使用 使用 global 引入  基本不使用
// 也可以使用全局变量 $GLOBALS['变量名'] 来使用 
$message = '<h1>我是一个变量</h1>' ;
echo $message;

function msg () {
    // 基本不使用
    // global $message;
    echo $GLOBALS['message'];
};
msg();
?>
```

#### 检查变量

```php
<?php
//检测变量是否定义 返回值是 布尔类型 
var_dump(isset($name));
$name = 'xioake';
echo $name;
// 删除变量
unset($name);
// 返回 false 就说明删除成功
var_dump(isset($name));
?>
```

### 字符串

#### 字符串连接

```php
<?php
$msg1 = 'xiaobai';
$msg2 = '今天';
$msg3 = '很开心';
// 用点 进行连接
echo $msg1."\t". $msg2."\t".$msg3;
```

#### 获取字符长度

```php
<?php

$msg = 'xiaobai';

// 获取字符串的长度
echo strlen($msg);

echo '<hr>';

$info = '小白';
// 如果是中文 一个汉字 占3个字符
echo strlen($info);
echo '<hr>';
// 如果使用mb_strlen() 来获取
echo mb_strlen($info);
```

#### 清除空格

```php
<?php
$msg = '    xkbk.top';
// 12
echo strlen($msg);
echo '<hr/>';
// 清除空格  第二个参数是 要删除的字符 默认去掉空格、制表符、换行符、回车符、垂直制表符等
echo strlen(trim($msg, ' .top'));  //
echo '<hr/>';
echo strlen(rtrim($msg));  // 删除右边
echo '<hr/>';
echo strlen(ltrim($msg));  // 删除右边
```

#### 字符哈希

```PHP
<?php
// md5 把字符 哈希成一个32位16进制的字符串
echo md5("xiaoke")
?>
```

#### 拆分字符串和合并字符串

```PHP
<?php
$msg = 'xkbk.top';
// 把字符串拆分 第一个参数是要拆分的符合 第二个是要拆分的数据
echo explode ('.', $msg )[0] ;

$info = ['email','xiaoke@88.com'];
echo "<hr>";
// 字符串合并
echo implode(':',$info);
```

#### 截取字符串

```PHP
<?php
$msg = '我是标题我只能显示20个字符';
// 第一个参数是 数据 第二个是从前面开始截取 第三个是从后开始 
echo mb_substr($msg,0,20,'utf8');
```

### 常量

```PHP
<?php
// define 定义常量 
// 定义的常量是不能改变的 
// 第一个参数是名称 第二个参数是数据 第三个是一个布尔值 true是不区分大小写 false是区分大小写
define('NAME','xiaoke',true);
// 这样子会报错
// define('NAME','xiaobai');
echo name;
echo '<hr>';
// 定义常量也可以使用 const
const MSG = '我是常量';
echo MSG;

echo '<hr>';

// 常量它不受访问限制

function info () {
    echo MSG;
};
info();
```

## 循环

#### for循环

```php
<?php
for($i= 0; $i<=100;$i++) {
    echo $i.'<hr/>';
};
?>
```

#### while循环

```php
<?php
$i = 100;
while ($i > 0) {    
	echo ($i--).'<hr>';
};
?>
```

#### do while 循环

```php
<?php
 $i = 0;
do {
    $i++;
    echo $i.'<hr/>';
    if($i>10) {
    break;
    }
}while(true);

?>
```

## 文件引入

#### include

```php
<?php
$nmae = '我是15.php的变量';
// include 可以使用前面的变量
// 如果模板文件不存在是报错，但是他会继续执行下面的代码
include 'index.html';
?>
```

#### require

```PHP
<?php
    //如果文件不存在，报错，且不会继续执行下面代码 
    require("default1.html");
    echo '1';
?>
```

## 函数

```PHP
<?php
function show ($nmae) {
    echo $nmae;
};
show('xioake');
```

## 数组

```PHP
<?php
// 索引数组
$arr1  = [1,2,3,4,5];
echo $arr1[0];

// 关联数组
$arr2  = [
    'name'=>'xiaobai',
    'age'=>'age'
];
echo $arr2['name'];
?>
```

#### 修改数组值

```PHP
<?php
// 索引数组
$name  = ['xiaobai'];
// 数据添加的第一种方法
$name[]  = 'xiaored';
$name[]  = 'xiaol';
print_r($name);


echo '<hr>';

// 关联数组
$arr = [
    [
    'name'=>'小白',
    'age'=>'15'
    ]
];

// 使用索引名称来修改值
$arr[0]['name'] = '小花';

print_r($arr[0])
?>
```

#### 数组循环

```PHP
<?php
$arr = ['xioake','xiaobai','xiaored','xiaopink'];
// 数组循环 第一个参数 数组名称 第二个自定义 key是索引
foreach($arr as $key=>$arrs) {
    echo $key.'='.$arrs.'<br>';
};
```

#### 数组删除添加统计

```php
<?php
$arr = ['xioake','xiaobai','xiaored','xiaopink'];
// 在数组后面添加数据
array_push($arr,'xiaohao');
// 删除数组的最后一个数据 返回值就是删除的数据
echo array_pop($arr).'<hr>';

// 在数组前面添加数据
array_unshift($arr,'小强');
// 删除数组的第一条数据
array_shift($arr);
print_r($arr);
echo '<hr>';

// 统计数组的总数 从1开始
echo count($arr);

```

### 获取时间

```php
<?php
//设置时区 北京时间
date_default_timezone_set('PRC');
// echo date_default_timezone_get();
// 获取时间
echo date('Y-m-d H:i:s');
```

### 生成随机验证码

```PHP
<?php 


function code (int $len = 5) : string 
{
    $str  = '123456789abcdefkjmnew';
    $code = '';
    for($i=0;$i<$len;$i++) {

        $index = mt_rand(0,strlen($str)-1);
        $code.= $str[$index];
    }
    return $code;

};
echo code();

```

# 文件操作

## 读取文件

- r    读取内容 文件不存在报错
- r+  读取+写入 
- w   写入内容 文件不存在不报错，系统会自动创建，如果存在文件，系统全部清空文件内容
- w+ 写入+读取
- a 写入 往后面追加内容 如果文件不存在，系统自动创建
- a+ 写入+读取
- x 写入 如果文件存在报错，不存在不报错系统会自动创建
- x+ 写入 + 读取

```php
<?php
 // fopen 打开文件 参数1是路径 参数2是打开方式 r 读取方式 注意：如果是想读取又想写入 打开方式是 r+
 // r 模式打开文件 如果文件不存在 它会报错 我们可以使用 file_exists('文件路径') 来判断文件是否存在 
 // w 模式打开文件 如果文件不存在 系统会自动给你创建文件 如果文件存在它会把文件里面的内容全部清空。
$handle = fopen('test.txt','r');
// fread 读取内容 参数1 已经打开好的文件返回值 参数2 读取的长度 filesize() 获取文件的长度
echo fread($handle,filesize('test.txt'));
```

## 写入内容

```php
<?php
 // r+ 读取和写入模式
$handle = fopen('test.txt','r+');
fwrite($handle,'小柯前端工程师');
```

