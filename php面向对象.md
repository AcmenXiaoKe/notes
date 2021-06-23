# PHP面向对象

> PHP的对象是通过类来生成出来的

基本使用

```php
<?php
class User
{
    public  function  index () {
        return 'Hello world';
    }
}
$user = new User;
echo $user->index();
```

