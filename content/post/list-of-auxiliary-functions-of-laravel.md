---
title: "Laravel的辅助函数列表"
date: 2018-02-28T00:09:37+08:00
typora-root-url: ..\..\static
tags: ["laravel", "辅助函数"]
categories: ["Laravel"]
draft: false
---

## 数组：

### array_add()

如果给的键不在数组中，则会把给定的键值添加到数组中：

```php
$array = array_add(['name' => 'Desk'], 'price', 100);

// ['name' => 'Desk', 'price' => 100]
```

这里合并数组有另外的一个方法：

```php
$person = ['name' => 'shea'] + ['age' => '22'] + ['age' => '22'];

// [["name"=>"shea"], ["age"=>"22"]]
```

### array_collapse()

将多个单个函数合并成一个数组：

```php
$array = array_collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

// [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### array_divide()

将返回两个数组，一个包含原始数组的键，另一个包含原始数组的值：

```php
list($keys, $values) = array_divide(['name' => 'Desk']);

// $keys: ['name']

// $values: ['Desk']
```

ps:这里的list(var,var)，用于在一次赋值中给一组变量赋值：

```php
$my_array = array("Dog","Cat","Horse");

list($a, $b, $c) = $my_array;
echo "I have several animals, a $a, a $b and a $c.";
```

### array_dot()

将多维数组平铺到一维数组中，点代表深度：

```php
$array = array_dot(['foo' => ['bar' => 'baz']]);

// ['foo.bar' => 'baz'];
```

### array_except()

从数组中删除给定的键值对：

```php
$array = ['name' => 'Desk', 'price' => 100];

$array = array_except($array, ['price']);

// ['name' => 'Desk']
```

### array_first()

返回数组中第一个通过给定测试的元素：

```php
$array = [100, 200, 300];

$value = array_first($array, function ($value, $key) {
    return $value >= 150;
});

// 200
```

将默认值作为第三个参数传给方法，如果没有值通过测试，则返回默认值：

```php
$value = array_first($array, $callback, $default);
```

### array_flatten()

将多为数组平铺为一维数组，这里返回值为数组的值：

```php
$array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

$array = array_flatten($array);

// ['Joe', 'PHP', 'Ruby'];
```

### array_forget()

使用点符号从深度嵌套的数组中移除给定的键值对：

```php
$array = ['products' => ['desk' => ['price' => 100]]];

array_forget($array, 'products.desk');

// ['products' => []]
```

### array_get()

使用点符号从深度嵌套的数组中检索值：

```php
$array = ['products' => ['desk' => ['price' => 100]]];

$value = array_get($array, 'products.desk');

// ['price' => 100]
```

可接收默认值作为第三个参数：

```php
$value = array_get($array, 'names.john', 'default');
```

### array_has()

使用点符号检查数组中是否存在给定的项目或项目组：

```php
$array = ['product' => ['name' => 'desk', 'price' => 100]];

$hasItem = array_has($array, 'product.name');

// true

$hasItems = array_has($array, ['product.price', 'product.discount']);

// false
```

### array_last()

返回数组中通过指定测试的最后一个元素：

```php
$array = [100, 200, 300, 110];

$value = array_last($array, function ($value, $key) {
    return $value >= 150;
});

// 300
```

可有默认值：

```php
$last = array_last($array, $callback, $default);
```

### array_only()

仅返回给定数组中指定的键值对：

```php
$array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

$array = array_only($array, ['name', 'price']);

// ['name' => 'Desk', 'price' => 100]
```

### array_pluck()

从数组中检索给定数组的所有值：

```php
$array = [
    ['developer' => ['id' => 1, 'name' => 'Taylor']],
    ['developer' => ['id' => 2, 'name' => 'Abigail']],
];

$array = array_pluck($array, 'developer.name');

// ['Taylor', 'Abigail'];
```

也可指定生成的列表的键：

```php
$array = array_pluck($array, 'developer.name', 'developer.id');

// [1 => 'Taylor', 2 => 'Abigail'];
```

### array_prepend()

将一个项目推送到数组开头，插入值并推送：

```php
$array = ['one', 'two', 'three', 'four'];

$array = array_prepend($array, 'zero');

// $array: ['zero', 'one', 'two', 'three', 'four']
```

也可指定该值的键：

```php
$array = ['price' => 100];

$array = array_prepend($array, 'Desk', 'name');

// ['name' => 'Desk', 'price' => 100]
```

### array_pull()

返回并从数组中删除键值对：

```php
$array = ['name' => 'Desk', 'price' => 100];

$name = array_pull($array, 'name');

// $name: Desk

// $array: ['price' => 100]
```

可有默认值：

```php
$value = array_pull($array, $key, $default);
```

### array_random()

从数组中返回随机值：

```php
$array = [1, 2, 3, 4, 5];

$random = array_random($array);

// 4 - (随机获取)
```

也可指定返回的数量，返回的为一个数组：

```php
$items = array_random($array, 2);

// [2, 5] - (随机获取)
```

### array_set()

使用点符号在深度嵌套的数组中设置一个值：

```php
$array = ['products' => ['desk' => ['price' => 100]]];

array_set($array, 'products.desk.price', 200);

// ['products' => ['desk' => ['price' => 200]]]
```

### array_sort()

按照其值排序数组：

```php
$array = ['Desk', 'Table', 'Chair'];

$sorted = array_sort($array);

// ['Chair', 'Desk', 'Table']
```

也可以按给定的闭包返回的结果对数组进行排序：

```php
$array = [
    ['name' => 'Desk'],
    ['name' => 'Table'],
    ['name' => 'Chair'],
];

$sorted = array_values(array_sort($array, function ($value) {
    return $value['name'];
}));

/*
    [
        ['name' => 'Chair'],
        ['name' => 'Desk'],
        ['name' => 'Table'],
    ]
*/
```

### array_sort_recursive()

使用sort函数递归排序数组：

```php
$array = [
    ['Roman', 'Taylor', 'Li'],
    ['PHP', 'Ruby', 'JavaScript'],
];

$sorted = array_sort_recursive($array);

/*
    [
        ['Li', 'Roman', 'Taylor'],
        ['JavaScript', 'PHP', 'Ruby'],
    ]
*/
```

### array_where()

使用给定的闭包来过滤数组：

```php
$array = [100, '200', 300, '400', 500];

$array = array_where($array, function ($value, $key) {
    return is_string($value);
});

// [1 => 200, 3 => 400]
```

### array_wrap()

将给定的值包装成一个数组，如果给定的值已经为数组，则不变：

```php
$string = 'Laravel';

$array = array_wrap($string);

// [0 => 'Laravel']
```

### data_fill()

使用点符号在给的嵌套数组或对象中设置缺少的值：

```php
$data = ['products' => ['desk' => ['price' => 100]]];

data_fill($data, 'products.desk.price', 200);

// ['products' => ['desk' => ['price' => 100]]]   未改变值

data_fill($data, 'products.desk.discount', 10);

// ['products' => ['desk' => ['price' => 100, 'discount' => 10]]]   改变了值
```

也可以使用 * 作为通配符，并相应的填入值：

```php
$data = [
    'products' => [
        ['name' => 'Desk 1', 'price' => 100],
        ['name' => 'Desk 2'],
    ],
];

data_fill($data, 'products.*.price', 200);

/*
    [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2', 'price' => 200],
        ],
    ]
*/
```

### data_get()

使用点符号从嵌套数值或对象中检索值：

```php
$data = ['products' => ['desk' => ['price' => 100]]];

$price = data_get($data, 'products.desk.price');

// 100
```

可接收默认值：

```php
$discount = data_get($data, 'products.desk.discount', 0);

// 0
```

### data_set()

使用点符号设置嵌套路由或对象的一个值：

```php
$data = ['products' => ['desk' => ['price' => 100]]];

data_set($data, 'products.desk.price', 200);

// ['products' => ['desk' => ['price' => 200]]]
```

可接收通配符 * ：

```php
    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2', 'price' => 150],
        ],
    ];

    data_set($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 200],
                ['name' => 'Desk 2', 'price' => 200],
            ],
        ]
    */
```

默认情况下，所有的值都将被覆盖，如果只想设置一个不存在的值，可传入flase作为第三个参数：

```php
$data = ['products' => ['desk' => ['price' => 100]]];

data_set($data, 'products.desk.price', 200, false);

// ['products' => ['desk' => ['price' => 100]]]
```

### head()

返回给定数组的第一个元素：

```php
$array = [100, 200, 300];

$first = head($array);

// 100
```

### last()

返回给定数组的最后一个元素：

```php
$array = [100, 200, 300];

$last = last($array);

// 300
```

## 路径：

### app_path()

返回app目录的完整路径，还可使用此函数来生成相对于app目录的文件完整目录路径：

```php
$path = app_path();php

$path = app_path('Http/Controllers/Controller.php');
```

### base_path()

返回项目根目录的完整路径，还可使用此函数生成相对于项目根目录的完整目录路径：

```php
$path = base_path();

$path = base_path('vendor/bin');
```

### config_path()

返回应用程序配置目录的完整路径，还可使用此函数来生成相对于应用程序配置目录的文件完整目录路径：

```php
$path = config_path();
```

### database_path()

返回应用程序数据库目录的完整路径，还可使用此函数来生成相对于应用程序数据库目录的文件完整目录路径：

```php
$path = database_path();
```

### mix()

获取mix版本化文件的路径：

```php
mix($file);
```

### public_path()

返回应用程序public目录的完整路径，还可使用此函数来生成相对于应用程序public目录的文件完整目录路径：

```php
$path = public_path();
```

### resourse_path()

返回应用程序resourse目录的完整路径，还可使用此函数来生成相对于应用程序resourse目录的文件完整目录路径：

```php
$path = resource_path();

$path = resource_path('assets/sass/app.scss');
```

### storage_path()

返回应用程序storage目录的完整路径，还可使用此函数来生成相对于应用程序storage目录的文件完整目录路径：

```php
$path = storage_path();

$path = storage_path('app/file.txt');
```