---
title: Java三目运算符空指针异常
date: 2016-08-25 15:00:39
categories: Java
tags:
- Java
---

今天在解决线上bug， 一不小心跳入了Java三目运算符自动拆箱和封箱的坑里面，遇到了NullPointerException的异常。

具体代码类似于下面：

~~~Java
public class Hello {
    public static void main(String[] args) {
        Bar bar = new Bar();
        Foo foo = new Foo();
        foo.setFoo(bar == null ? 0 : bar.getBar());
    }
}
class Foo {
    private Integer foo;
    public Integer getFoo() {
        return foo;
    }
    public void setFoo(Integer foo) {
        this.foo = foo;
    }
}
class Bar {
    private Integer bar;
    public Integer getBar() {
        return bar;
    }
    public void setBar(Integer bar) {
        this.bar = bar;
    }
}
~~~

运行之后，说这行代码空指针`foo.setFoo(bar == null ? 0 : bar.getBar());`。细看了下，这行代码没有什么问题，唯一有可能出问题的就是三目运算符这里。

后来把三目运算符变成if-else就没问题了。

~~~Java
if (bar == null) {
    foo.setFoo(0);
} else {
    foo.setFoo(bar.getBar());
}
~~~

尽管这样没有异常了，但是还是不知道为什么三目运算符就不行。

后来搜索后，发现这边文章 [自动拆箱导致空指针异常](http://www.hollischuang.com/archives/435)，才知道原因在于三目运算符的第二位和第三位中，一个是基本类型，一个是对象类型，三目运算符会做自动拆箱和封箱导致。即`bar.getBar() -> null.intValue() -> NullPointerException`

因此，这个bug的另外一个解决方案，就是不用拆成if-else，而是把第二位和第三位都变成对象类型就行：

~~~Java
foo.setFoo(bar == null ? Integer.valueOf(0) : bar.getBar());
~~~

