---
title: Java Runtime 动态代理
date: 2016-06-01 15:20:54
categories: Java
tags:
- Java
- Android
- Runtime
- 动态代理
---

使用过Spring的朋友应该对AOP『Aspected Oriented Programe』很熟悉，那么是否被里面的前置增强、后置增强、环绕增强所震撼呢？有没有想过这背后的技术实现呢？其实JDK的动态代理就能实现。下面做一个简单的示例，来阐述如何在Runtime时，动态增强原有的方法。

## 前提环境

假设我们有一个`Waiter`类，里面含有两个方法:

- welcome() // 欢迎光临!
- bye() // 谢谢惠顾!

```
欢迎光临!
谢谢惠顾!

Process finished with exit code 0
```
  
后来发现欢迎顾客和送别顾客不够礼貌，需要在欢迎之前说『您好！』，送别顾客后说『祝您愉快!』。但我们不希望动原有的业务类`Waiter`，那么如何实现呢？

## 实现步骤

1. 创建一个业务对象『必须要有接口』, 执行业务方法
  
  ```Java
  public interface Receptor {
      void welcome();
      void bye();
  }
  ```
  ```Java
  public class Waiter implements Receptor{
      @Override
      public void welcome() {
          System.out.println("欢迎光临!");
      }

      @Override
      public void bye() {
          System.out.println("谢谢惠顾!");
      }
}
  ```
1. 创建一个具有公用代码的对象，并将这些公用代码声明成方法

  ```Java
  public class PoliteWords {
      public void sayHello() {
          System.out.println("您好!");
      }
      public void sayHappy() {
          System.out.println("祝您愉快!");
      }
  }
  ```
1. 创建一个可以使用公用代码对象的对象，这个对象可以对要被处理的目标对象实施方法拦截，执行公用代码对象中的方法。借助JDK提供的API『InvocationHandler』

  ```Java
  public class ReceptorHandler implements InvocationHandler {
      // 省略...

      /**
       * 重写invoke方法,从而达到增强目标对象的目的
       *
       * @param proxy 增强后的对象
       * @param method 目标对象上正在被调用的方法
       * @param args 目标对象上正在被调用的方法所传递的参数
       * @return 目标对象正在被调用的方法执行的结果
       * @throws Throwable
       */
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          Object result;
          if (method.getName().equals("welcome")) {// welcome之前sayHello
              interceptor.sayHello();
              result = method.invoke(target, args);
          } else if (method.getName().equals("bye")) {// bye之后sayHappy
              result = method.invoke(target, args);
              interceptor.sayHappy();
          } else {
              result = method.invoke(target, args);
          }
          return result;
      }
  }
  ```
1. 创建一个可以获取增强了功能之后对象的代理工厂，工厂的作用就是返回增强后的代理Proxy, 他的类型应该和目标对象的接口类型相同。

  ```java
  public class ReceptorProxyFactory {
      public static Object getProxy(Object target) {
          ReceptorHandler handler = new ReceptorHandler();
          handler.setTarget(target);

          return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), handler);
      }
  }
  ```
1. 编写测试类（可选）

  ```Java
  public class TestProxy {
      public static void main(String[] args) {
          Waiter target = new Waiter();
          Receptor receptor = (Receptor) ReceptorProxyFactory.getProxy(target);
          receptor.welcome();
          receptor.bye();
      }
  }
  ```

具体的示例源码，移步 [Github-helloJavaSE](https://github.com/wangsheng/helloJavaSE)

## 执行结果

```
您好!
欢迎光临!
谢谢惠顾!
祝您愉快!

Process finished with exit code 0
```

**注意:** JDK动态代理只能代理接口类型的对象，并且返回接口类型。如果要增强那些没有接口的对象，JDK动态代理不能实现，要依赖 [CGLIB](https://github.com/cglib/cglib) 技术。
