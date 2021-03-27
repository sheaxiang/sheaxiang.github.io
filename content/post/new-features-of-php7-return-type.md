---
title: "PHP7的新特性详解–返回类型（Return Types）"
date: 2018-02-23T23:51:17+08:00
typora-root-url: ..\..\static
tags: ["新特性", "php7", "返回类型"]
categories: ["PHP"]
draft: false
---

## PHP 5.x

目前在PHP 5.x中，没有办法来指定一个方法应该返回哪种类型的结果，你可能会说，可以转换返回的值啊，我们可以猜测返回结果的具体类型，但它并不理想且容易遇到`意想不到`的怪事。

```php
function getId() { 
    return (int) $this->id; 
}
```

## PHP 7.x

PHP 7很容易做到，我们只需要在定义方法时指明返回得类型即可。

```php
function getUser() : User { 
    return new User; 
}
```

在上面的例子中，如果我们试图返回任何其他非`User的结果`，那么我们就会遇到一个TypeException。

```php
function getUser() : User { 
    return 'Mince Pies!'; 
} 
$user = getUser();
```

```base
TypeException: Return value of getUser() must be an instance of User, string returned
```

## 接口

我们也可以定义我们的接口，要求其使用指定的返回类型。

```php
interface UserInterface { 
    public function getUser() : User; 
    public function getUserProfile() : UserProfile; 
}
```

我们就能够通过设定返回类型的typehints的方式，来实现该接口。

```php
class UserRepository implements UserInterface { 
    public function getUser() : User { 
        return new User; 
    } 
    public function getUserProfile() : UserProfile { 
        return new UserProfile; 
    } 
} 
(new UserRepository)->getUser();
```

我喜欢这种新方法; 那感觉就像做事情用了一个自然而简洁的方式。但如果我们仅实现该接口而不调用，让方法返回期望之外的结果会如何呢？

```php
class UserRepository implements UserInterface { 
    public function getUser() : User { 
        return 'foo'; 
    } 
    public function getUserProfile() : UserProfile { 
        return 'bar'; 
    } 
}
```

在这一点上的代码不会错误，因为我们还没有调用该方法，应用程序只能在运行时，当返回`foo`和`bar`的字符串时才抛出一个异常。

```php
(new UserRepository)->getUser();
```

```base
Uncaught TypeException: Return value of UserRepository::getUser() must be an instance of User, string returned
```

## 更多栗子

```php
<?php
    //有效的返回类型
   declare(strict_types = 1);
   function returnIntValue(int $value): int {
      return $value;
   }
   print(returnIntValue(5));
?>
```

```php
<?php
    //无效的返回类型
   declare(strict_types = 1);
   function returnIntValue(int $value): int {
      return $value + 1.0;
   }
   print(returnIntValue(5));
?>
```

这将在浏览器产生输出以下结果：

```base
Fatal error: Uncaught TypeError: Return value of returnIntValue() must be of the type integer, float returned
```