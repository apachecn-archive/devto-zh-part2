# 教程:如何在 PHP 中递归合并两个对象

> 原文：<https://dev.to/joshualjohnson/tutorial-how-to-recursively-merge-two-objects-in-php-3jf9>

我最近在做一个项目，发现我必须用 PHP 合并两个非常复杂的对象。我去搜索解决方案，当然找到了`array_merge()`和`array_merge_recursively()`。所以很自然地，我想我应该从把我的对象转换成数组并递归地使用 array _ merge _ recursively 开始。为此我取得了以下成绩:

```
class Utils {
    /**
     * Recursively merges two objects and returns a resulting object.
     * @param object $obj1 The base object
     * @param object $obj2 The merge object
     * @return object The merged object
     */
    public function mergeObjectsRecursively($obj1, $obj2)
    {
        $baseObject = (array) $obj1;
        $mergeObject = (array) $obj2;
        $merged = array_merge_recursive($baseObject, $mergeObject);
        return (object) $merged;
    }
}

$utils = new Utils();

$obj1 = (object) [
    'debug' => true,
    'routes' => (object) [
        'test' => '/my-tests'
    ]
];

$obj2 = (object) [
    'routes' => (object) [
        'test' => '/my-tests-replaced'
    ]
];

$obj3 = $utils->mergeObjectsRecursively($obj1, $obj2);

var_dump($obj3);

/**
 * Resulting Object:
 * object(stdClass)#6 (2) {
 *   ["debug"]=>
 *   bool(true)
 *   ["routes"]=>
 *   array(1) {
 *     ["test"]=>
 *     array(2) {
 *       [0]=>
 *       string(9) "/my-tests"
 *       [1]=>
 *       string(18) "/my-tests-replaced"
 *     }
 *   }
 * }
 */ 
```

Enter fullscreen mode Exit fullscreen mode

注意，在上面的方法中，得到的对象是一个具有“routes”属性的对象，它是从一个对象转换成一个数组的。不仅如此，`obj->routes->test`值还被组合成一个值数组。

**不能接受！**

## 答案:递归合并两个 PHP 对象

我们需要采取的方法是创建我们自己的合并逻辑，这样它将覆盖值而不是合并它们。下面是新的方法:

```
class Utils {

    /**
     * Recursively merges two objects and returns a resulting object.
     * @param object $obj1 The base object
     * @param object $obj2 The merge object
     * @return object The merged object
     */
    public function mergeObjectsRecursively($obj1, $obj2)
    {
        $merged = $this->_mergeRecursively($obj1, $obj2);
        return $merged;
    }

    /**
     * Recursively merges two objects and returns a resulting object.
     * @param object $obj1 The base object
     * @param object $obj2 The merge object
     * @return object The merged object
     */
    private function _mergeRecursively($obj1, $obj2) {
        if (is_object($obj2)) {
            $keys = array_keys(get_object_vars($obj2));
            foreach ($keys as $key) {
                if (
                    isset($obj1->{$key})
                    && is_object($obj1->{$key})
                    && is_object($obj2->{$key})
                ) {
                    $obj1->{$key} = $this->_mergeRecursively($obj1->{$key}, $obj2->{$key});
                } elseif (isset($obj1->{$key})
                && is_array($obj1->{$key})
                && is_array($obj2->{$key})) {
                    $obj1->{$key} = $this->_mergeRecursively($obj1->{$key}, $obj2->{$key});
                } else {
                    $obj1->{$key} = $obj2->{$key};
                }
            }
        } elseif (is_array($obj2)) {
            if (
                is_array($obj1)
                && is_array($obj2)
            ) {
                $obj1 = array_merge_recursive($obj1, $obj2);
            } else {
                $obj1 = $obj2;
            }
        }

        return $obj1;
    }
}

$utils = new Utils();

$obj1 = (object) [
    'debug' => true,
    'modules' => [
        'Module1'
    ],
    'routes' => (object) [
        'test' => '/my-tests',
        'override' => 'false',
        'test2' => (object) [
            'override' => 'false',
            'shouldStay' => 'true'
        ]
    ]
];

$obj2 = (object) [
    'debug' => false,
    'modules' => [
        'Module2',
        'Module3'
    ],
    'routes' => (object) [
        'route1' => (object) [
            'path' => '/'
        ],
        'override' => 'true',
        'test2' => (object) [
            'override' => 'true'
        ]
    ]
];

$obj3 = $utils->mergeObjectsRecursively($obj1, $obj2);

var_dump($obj3);

/**
 * Resulting Object:
 *  object(stdClass)#4 (3) {
 *    ["debug"]=>
 *    bool(false)
 *    ["modules"]=>
 *    array(3) {
 *      [0]=>
 *      string(7) "Module1"
 *      [1]=>
 *      string(7) "Module2"
 *      [2]=>
 *      string(7) "Module3"
 *    }
 *    ["routes"]=>
 *    object(stdClass)#3 (4) {
 *      ["test"]=>
 *      string(9) "/my-tests"
 *      ["override"]=>
 *      string(4) "true"
 *      ["test2"]=>
 *      object(stdClass)#2 (2) {
 *        ["override"]=>
 *        string(4) "true"
 *        ["shouldStay"]=>
 *        string(4) "true"
 *      }
 *      ["route1"]=>
 *      object(stdClass)#5 (1) {
 *        ["path"]=>
 *        string(1) "/"
 *      }
 *    }
 *  }
 * / 
```

Enter fullscreen mode Exit fullscreen mode

请注意，生成的对象保持不变。`$obj->routes->test`包含被替换的值，而`$obj->routes`和`$obj->routes->test`仍然是一个对象。

尽情享受吧！

帖子[教程:如何在 PHP 中递归合并两个对象](https://ua1.us/news/tutorials/tutorial-how-to-recursively-merge-two-objects-in-php/)最早出现在 [UA1 实验室](https://ua1.us)。