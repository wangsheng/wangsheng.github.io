---
title: PHP数据类型声明那些事
date: 2019-05-21 18:22:40
categories: PHP
tags:
- PHP
---

对于PHP函数的入参，从5.0开始，引入了array类型，来指定所传参数必须为数组。从7.0开始，又引入了bool、float、int、string，分别来指定所传的参数必须为布尔、浮点、整型和字符串。而且7.0同时引入了可以声明函数返回类型的新特性。

这种新特性的引入，其实隐性表达了PHP逐渐超强类型转换，因为强类型可以增强程序运行时的健壮性，以及重构时静态代码分析更方便。

所以借助PHP 7.0这样的新特性，就可以像强类型语言一样写代码了：

1. 生日类
  ~~~PHP
  class Birthday
  {
      /** @var  int 年 */
      private $year;

      /** @var  int 月 */
      private $month;

      /** @var  int 日 */
      private $day;

      /**
       * Birthday constructor.
       * @param int $year
       * @param int $month
       * @param int $day
       */
      public function __construct($year, $month, $day)
      {
          $this->year = $year;
          $this->month = $month;
          $this->day = $day;
      }

      function __toString()
      {
          return "$this->year-$this->month-$this->day";
      }
  }
  ~~~
2. 人类
  ~~~PHP
  class Person
  {
      /** @var  string 名字 */
      private $name;

      /** @var  Birthday 出生日期 */
      private $birthday;

      /** @var  string 简介 */
      private $profile;

      /**
       * @return string
       */
      public function getName(): string
      {
          return $this->name;
      }

      /**
       * @param string $name
       */
      public function setName(string $name)
      {
          $this->name = $name;
      }

      /**
       * @return Birthday
       */
      public function getBirthday(): Birthday
      {
          return $this->birthday;
      }

      /**
       * @param Birthday $birthday
       */
      public function setBirthday(Birthday $birthday)
      {
          $this->birthday = $birthday;
      }

      /**
       * @return string
       */
      public function getProfile(): string
      {
          return $this->profile;
      }

      /**
       * @param string $profile
       */
      public function setProfile(string $profile)
      {
          $this->profile = $profile;
      }
  }
  ~~~

3. 测试代码
  ~~~PHP
  $birthday = new Birthday(2018, 10, 10);

  $person = new Person();
  $person->setName("张三");
  $person->setBirthday($birthday);
  $person->setProfile("Hi, Nice to meet you!");

  dump($person->getName());
  dump($person->getBirthday());
  dump($person->getProfile());
  ~~~

4. 执行结果

   ~~~Shell
   "张三"
   "2018-10-10"
   "Hi, Nice to meet you!"
   ~~~
5. 但是，这里有一个坑。比方说姓名是必填的，这个没疑问，但是有的用户不填写生日或者简介，那么这时候会发生什么情况呢？
  ~~~PHP
  $person = new Person();
  $person->setName("李四");
  $person->setBirthday(null); //Exception 'TypeError' with message 'Argument 1 passed to Person::setBirthday() must be an instance of Birthday, null given, called in demo.php on line 46'
  $person->setProfile(null); //Exception 'TypeError' with message 'Argument 1 passed to Person::setProfile() must be of the type string, null given, called in demo.php on line 47'

  dump($person->getName());
  dump($person->getBirthday()); // Exception 'TypeError' with message 'Return value of Person::getBirthday() must be an instance of Birthday, null returned'
  dump($person->getProfile()); // Exception 'TypeError' with message 'Return value of Person::getProfile() must be of the type string, null returned'
  ~~~

因为在php，NULL对象跟自定义类的对象或者string对象不是同一种类型，所以不管是赋值还是返回值都会报类型错误。这时候就无可奈何地把声明的类型一个一个删掉了。一脸嫌弃的样子飘过(Java的同学甚至会感到莫名其妙)。

## 官方改进

估计是收到吐槽多了，PHP在7.1版本立马加入了`Nullable types` 类型，用法很简单，在原有的入参类型或者返回类型前面加入`?`即可。比方说`?string`代表，既可以传递string类型的值，也可以传递NULL类型的值。返回值也一样。有了该特性，我们就又能按照强类型的思路编程了，那么Person类就可以这么写了：

   ~~~PHP
class Person
{
  /** @var  string 名字 */
  private $name;

  /** @var  Birthday 出生日期 */
  private $birthday;

  /** @var  string 简介 */
  private $profile;

  /**
       * @return string
       */
  public function getName(): string
  {
    return $this->name;
  }

  /**
   * @param string $name
  */
  public function setName(string $name)
  {
    $this->name = $name;
  }

  /**
   * @return Birthday
  */
  public function getBirthday(): ?Birthday
  {
    return $this->birthday;
  }

  /**
   * @param Birthday $birthday
  */
  public function setBirthday(?Birthday $birthday)
  {
    $this->birthday = $birthday;
  }

  /**
   * @return string
  */
  public function getProfile(): ?string
  {
    return $this->profile;
  }

  /**
   * @param string $profile
  */
  public function setProfile(?string $profile)
  {
    $this->profile = $profile;
  }

}
   ~~~

拿李四的数据，再跑一次

  ~~~PHP
$person = new Person();
$person->setName("李四");
$person->setBirthday(null);
$person->setProfile(null);

dump($person->getName());
dump($person->getBirthday());
dump($person->getProfile());
  ~~~

输出如下:

  ~~~Shell
"李四"
null
null
  ~~~

可以看出，在原有代码里，把哪些可能会出现null的属性前面添加`?`之后，类型异常的问题就解决了，这样调用方也可以顺利地调用底层模块了。你好，我好，大家好！