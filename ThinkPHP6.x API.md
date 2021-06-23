# ThinkPHP6.x API

## 命令

### 创建资源控制器

> 模块@控制器
>
> 如果只是用于接口开发 可以加上 --api

```
php think make:controller Index@User --api
```

## 资源路由

对于资源控制器，只要设置一个路由，即可得到所有资源操作的路由方式

```php
//用户模块资源路由 
Route::resource('user', 'User');
```

PS：具体生成的路由如下：

| 标识   | 请求类型 | 生成路由规则 | 对应操作方法（默认） |
| ------ | -------- | ------------ | -------------------- |
| index  | GET      | user         | index                |
| save   | POST     | user         | save                 |
| read   | GET      | user/:id     | read                 |
| update | PUT      | user/:id     | update               |
| delete | DELETE   | user/:id     | delete               |

## 生成标准 API

### 创建 api 基类 

```php
abstract class Base
{
    public function create( $data, $msg='', $code=200, $type ='json') {
    // 返回格式
        $result = [
            'code'  =>  $code,
            'msg'   =>  $msg,
            'data'  =>  $data
        ];
        return Response::create($result,$type);
    }
}
```

PS：标准的 api 格式有三个部分组成：code(状态码)、msg(消息)、data(数据)

PS：在 data 数据获取上，单一数据为：data:{}，数据列表为：data:[{},{}]

### 生成 api 接口 

```php
 public function index()
    {
        $data = UserModel::field(['id','username','email'])->select();
        return $this->create($data,'请求成功！');
    }
```

## 分页

### 自动

 对于传统的 Web 分页，提供了一个 paginate(5)自动生成分页的方法； 

```php
$data = UserModel::field(['id','username','email'])->paginate(5);
```

PS：这种方法非常方便，可以生成各种数据，有对这些数据有需求的前端可以考虑；

### 手动

 如果只是简洁的获取分页，然后通过 page(页码)和 page_size(条数)获取； 

直接采用 page()方法，这个方法来源于 limit()方法，可以手动控制的灵活些；

```php
->page($this->page,$this->pageSize)->paginate();
```

对于 page，内部会自动判断，pageSize 可以在 config 设置默认值； 

在基类 Base.php 中编写分页的方法，用于以后更多模块； 

## JWT 

### 安装

```
composer require firebase/php-jwt
```

### 生成

```php
// 应用公共文件
use \Firebase\JWT\JWT;
//生成token
function createJwt($userId = 'zq')
{
    $key = md5('xiaoke@88'); //jwt的签发密钥，验证token的时候需要用到
    $time = time(); //签发时间
    $expire = $time + 14400; //过期时间
    $token = array(
        "user_id" => $userId,
        "iss" => "http://www.najingquan.com/",//签发组织
        "aud" => "xiaoke", //签发作者
        "iat" => $time,
        "nbf" => $time,
        "exp" => $expire
    );
    $jwt = JWT::encode($token, $key);
    return $jwt;
}
```

### 验证

路由验证

```php
Route::group(function (){
// 需要验证的路由列表
Route::post('guard','Guard/index');

})
    // 路由验证中间件
    ->middleware(function ($request,Closure $next) {
        // 获取到 请求头
    $header = \think\facade\Request::header();
    $token = array_key_exists('authorization',$header) ? $header['authorization'] : '';
    // 密钥
    $key = md5('xiaoke@88');
    try {
        $jwtAuth = json_encode(JWT::decode($token, $key, array('HS256')));
        $authInfo = json_decode($jwtAuth, true);
        if (!empty($authInfo['user_id'])) {
            return $next($request);
        }
        $msg = [
                'status' => 404,
                'msg' => 'Token验证不通过,用户不存在'
        ];
        return json($msg);
    }  catch (\Firebase\JWT\ExpiredException $e) {
        return json([
            'status' => 400,
            'msg' => 'Token过期'
        ]);
    } catch (\Exception $e) {
        return json([
            'status' => 400,
            'msg' => 'Token无效'
        ]);
    }
});
```

