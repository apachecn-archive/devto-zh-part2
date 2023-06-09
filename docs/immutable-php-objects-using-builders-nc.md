# 使用构建器的不可变 PHP 对象

> 原文：<https://dev.to/cr0wst/immutable-php-objects-using-builders-nc>

前几天，我讨论了在 PHP 中构建数据对象时可能出现的一些问题。到目前为止，我的经验使我相信 PHP 中的数据对象是一个相当新的概念，因为 PHP 语言:

1.  不是强类型的——导致很多滥用。
2.  允许魔法赋值——这意味着对象的定义结构可以在代码执行过程中改变。

考虑你的基本 POPO，还是**P**lain**O**LD**P**HP**O**subject:

```
class Person {
    protected $firstName;
    protected $lastName;

    public function setFirstName(string $firstName): void
    {
        $this->firstName = $firstName;
    }

    public function setLastName(string $lastName): void
    {
        $this->lastName = $lastName;
    }

    public function getFirstName(): string
    {
        return $this->firstName;
    }

    public function getLastName(): string
    {
        return $this->lastName;
    }
} 
```

这个对象代表一个人，带有所有的基本标识符。您可以实例化一个人并设置名字和姓氏。然而，这个 POPO 是完全可变的，您可以随时调用 setters 并修改信息。事实上，这个对象没有带参数的构造函数，所以为了实例化它，您*需要*使用提供的 setters。

## 引入不变性

在我们的讨论中，需要对象的不变性。我们想给用户一种方法来构造一个`Person`对象，并且一旦构造好就不能修改它。这里可以使用一些模式，但是为了使事情尽可能简单，这个类变成了这样:

```
class Person {
    protected $firstName;
    protected $lastName;

    public function __construct(string $firstName, string $lastName)
    {
        $this->firstName = $firstName;
        $this->lastName = $lastName;
    }

    public function getFirstName(): string
    {
        return $this->firstName;
    }

    public function getLastName(): string
    {
        return $this->lastName;
    }
} 
```

定居的人被放逐了。生成的对象将允许您在实例化时设置属性，并且在没有创建新对象的情况下不会修改这些属性。你知道，这是用构造函数 101 进行的面向对象编程。这是一个完全可以接受的模式。在我们的系统中，一个人必须有一个名和一个姓，这种做法被严格执行。

## 进化中的人

当这个人开始长大时会发生什么？当他们开始衰老时会发生什么？那么中间名、身高、体重、性别以及我们可能想要追踪的任何其他属性呢？如果这些字段中只有一部分是必需的呢？留给我们这样的东西:

```
class Person {
    protected $firstName;
    protected $lastName;
    protected $middleName;
    protected $age;
    protected $gender;
    protected $height;
    protected $weight;

    public function __construct(
        string $firstName,
        string $lastName,
        ?string $middleName = null,
        int $age = 0,
        ?string $gender = null,
        int $height = 0,
        int $weight = 0
    ) {
        $this->firstName = $firstName;
        $this->lastName = $lastName;
        $this->middleName = $middleName;
        $this->age = $age;
        $this->gender = $gender;
        $this->height = $height;
        $this->weight = $weight;
    }

    public function getFirstName(): string
    {
        return $this->firstName;
    }

    public function getLastName(): string
    {
        return $this->lastName;
    }

    public function getMiddleName(): ?string
    {
        return $this->middleName;
    }

    public function getAge(): int
    {
        return $this->age;
    }

    public function getGender(): ?string
    {
        return $this->gender;
    }

    public function getHeight(): int
    {
        return $this->height;
    }

    public function getWeight(): int
    {
        return $this->weight;
    }
} 
```

你能认识到这里固有的问题吗？构造函数参数的数量已接近不可接受的水平。不仅如此，这个很难用。如果我想代表一个没有中间名但有年龄的人，我必须做这样的事情:

```
$person = new Person('John', 'Doe', null, 32); 
```

但是史蒂夫，你是说，我们必须保持不变性！有没有一种方法可以让*构建*这样一个对象，同时仍然使其属性为只读？有！

## 建设者

构建器可以用来构建类。您可以指导他们(通常使用 fluent 接口)设置类的哪些属性。然后，您调用`build()`方法，新构造的对象返回给您。这里有一个`PersonBuilder`不起作用的例子:

```
class PersonBuilder {
    protected $person;

    public function __construct(string $firstName, string $lastName)
    {
        $person = new Person($firstName, $lastName);
    }

    public function middleName()
    {
        $this->person->middleName = $middleName;
        return $this;
    }

    // repeat for other properties

    public function build()
    {
        return $person;
    }
} 
```

我们首先用所需的字段构建`Person`。您可以通过从`Person`中移除构造函数来规避这个问题，但是我觉得这样可以确保仍然提供必需的字段。

然后，我们有一系列方法来设置`Person`上的指示属性，还有一个`build()`方法返回结果。注意到问题了吗？没错，`PersonBuilder`无法访问`Person`，因为它是不可变的。我们需要一种方法来存储关于建造者的信息，因为一旦这个人被建造了，就不允许对其进行任何改变。

### 第一个想法:将属性存储在一个数组中

事实上，在我开始写这个帖子之前，我没有想过这个想法。我不完全赞成，但我认为这不是个坏主意。我们可以在`PersonBuilder`上使用一个数组来存储我们最终将用来构造`Person`的属性。

```
class PersonBuilder {
    private $properties = [];

    public function __construct(string $firstName, string $lastName)
    {
        $this->properties['firstName'] = $firstName;
        $this->properties['lastName'] = $lastName;
    }

    public function middleName(string $middleName)
    {
        $this->properties['middleName'] = $middleName;
        return $this;
    }

    public function age(int $age)
    {
        $this->properties['age'] = $age;
        return $this;
    }

    // repeat for other properties

    public function build()
    {
        return new Person(
            $this->properties['firstName'],
            $this->properties['lastName'],
            $this->getPropertyOrDefault('middleName'),
            $this->getPropertyOrDefault('age', 0),
            $this->getPropertyOrDefault('gender'),
            $this->getPropertyOrDefault('height', 0),
            $this->getPropertyOrDefault('weight', 0)
        );
    }

    private function getPropertyOrDefault($key, $default = null)
    {
        return array_key_exists($key, $this->properties) ? $this->properties[$key] : $default;
    } 
```

这里的用法现在应该是:`$person = (new PersonBuilder('John', 'Doe'))->age(25)->build();`

> 注意:你可以在构建器上神奇地设置属性，而不是使用数组，因为 PHP 中的数组有点像对象。我不太喜欢魔法属性，尽管它确实减少了对`getPropertyOrDefault()`方法的需求。

这还不算太糟，但是它要求我们创建另一个类，并且我们必须存储这些属性。

### 第二个想法:将属性存储为属性

另一种可能是将属性存储在构建器上，就像属性一样。我在第一个想法中提到了这一点，所以我不会说得太详细，但我觉得这基本上创建了一个重复的对象。解决这个问题的一个方法是使用继承。大概是这样的:

```
class PersonBuilder extends Person {
    public function middleName(string $middleName)
    {
        $this->middleName = $middleName;
        return $this;
    }

    public function age(string $age)
    {
        $this->age = $age;
        return $this;
    }

    public function build()
    {
        // Required Fields
        $person = new Person($this->firstName, $this->lastName);

        // Optional Fields
        foreach (get_object_vars($this) as $key => $value) {
            $person->{$key} = $value;
        }

        return $person;
    }
} 
```

这是一种有趣的方法。我并不完全赞同它，因为我认为它以一种非理想的方式滥用了继承。它当然让我们可以直接访问`Person`变量，甚至允许我们迭代`Person`类的属性并给它们赋值。我们仍然需要创建一个独立的对象，它被绑定到`Person`，也是一个`Person`。

### 第三种想法:内在建造者

在 Java 中，你可以定义内部类。内部类包含在一个类中，可以访问外部类的所有属性。使用内部`Builder`类来构造外部类并不罕见。PHP 7 引入了匿名类，我认为我们可以以类似的方式利用它。它并不完美，而且和第二个想法有着同样的继承问题，但是我仍然认为它很棒。

下面是我想到的:

```
class Person {
    protected $firstName;
    protected $middleName;
    protected $lastName;
    protected $age;
    protected $gender;
    protected $height;

    protected function __construct(string $firstName, string $lastName)
    {
        $this->firstName = $firstName;
        $this->lastName = $lastName;
    }

    public function getFirstName(): string
    {
        return $this->firstName;
    }

    public function getMiddleName(): string
    {
        return $this->middleName;
    }

    public function getLastName(): string
    {
        return $this->lastName;
    }

    public function getAge(): int
    {
        return $this->age;
    }

    public function getGender(): string
    {
        return $this->gender;
    }

    public function getHeight(): int
    {
        return $this->height;
    }

        public static function Builder(string $firstName, string $lastName)
    {
        return new class($firstName, $lastName) extends Person {

            public function __construct(string $firstName, string $lastName)
            {
                $this->firstName = $firstName;
                $this->lastName = $lastName;
            }

            public function build()
            {
                $person = new parent($this->firstName, $this->lastName);
                foreach (get_object_vars($this) as $key => $value) {
                    $person->{$key} = $this->{$key};
                }
                return $person;
            }

            public function lastName(string $lastName)
            {
                $this->lastName = $lastName;
                return $this;
            }

            public function age(int $age)
            {
                $this->age = $age;
                return $this;
            }

            public function gender(string $gender)
            {
                $this->gender = $gender;
                return $this;
            }

            public function height(int $height)
            {
                $this->height = $height;
                return $this;
            }
        };
    }
} 
```

通过利用一个静态方法返回一个匿名的`Person`类的子类，我们可以内部化我们的构建器。虽然这仍然滥用了实例化，而且从技术上来说，它确实返回了一些**是**T1】的东西，但我觉得这种内部化仍然是一个不错的概念。我们的人现在可以流利地使用:

```
$person = Person::Builder('John', 'Doe')->age(18)->height(60)->build(); 
```

我们不必经历一个带有大量可选参数的总体构造函数，结果`Person`是不可变的。

## 结论

这只是我的一个有趣的想法，我相信关于 POPOs 是不可变的，以及这是否会使事情变得复杂，还有更大的讨论。不管怎样，这都是一个有趣的实验，用 PHP 编写了一个有趣的匿名类应用程序，虽然有点奇怪。