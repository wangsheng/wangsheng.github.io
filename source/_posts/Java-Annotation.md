---
title: Java Annotation
date: 2016-06-01 15:26:26
toc: true
categories: Java
tags:
- Java
- Annotation
---

Annotation是什么？维基百科: A form of syntactic metadaa that can be added to Java source code. 也就是说，Annotation的引入是为了从Java语言层面上，为Java源代码提供元数据的支持。[参见维基百科](http://en.wikipedia.org/wiki/Java_annotation)

## Annotation的用途

表象，替代之前JDK1.4开发中，大量繁琐的配置项，Annotation的出现其实可以极大简化配置文件的数量和需要关注配置的内容。但其实，注解带来的益处远不至于此。

## Annotation的分类

- 文档标注型

  主要是@Documented,用以标注是否在javadoc中
- 编译检查型

  主要在编译过程中，给Java编译器若干指令，检查Java代码中是否存在若干问题， 改变编译器的动作或者行为，通过Annotation的使用，可以调整和控制编译器的使用以及让编译器提供关于代码的更多的检查和验证。主要有：@Override，@SuppressWarning
- 代码分析型
  
  此类Annotation是在我们开发中使用最多的，主要是通过Annotation提供给代码更多的额外特性和设置，在Java运行过程中发挥作用。常见的是在Spring或者Hibernate等框架中出现的@Controller，@Service，@Bean， @Table, @Enitty等等.

## 自定义生命周期为Runtime类型Annotation

Runtime的处理主要依赖于反射的接口，在字节码中寻找Annotation的接口和输入参数，提取其内容和数值。大部分的情况下是基于代理模式，动态生成相应的代理类实例，然后通过代理类，调用相应的InvocationHandler，在InvocationHandler之内完成Annotation所要执行的动作；然后再继续调用原来的方法，继续执行。

**用户在定义Runtime类型的Annotation时，需要的步骤：**

- 定义Annotation
- 定义Annotation的处理器类『通过Reflect、InvocationHandler、Proxy.newProxyInstance()来处理具体的逻辑』
- 选择合适的调用Annotation的时机和切入点。

## 示例

![custom-annotation-demo.png](http://img.iaquam.com/image/custom-annotation-demo.png)

自定义一个@OnClick注解，通过在方法前面加入这个注解，从而为指定的组件动态添加事件监听方法。

- 定义OnClick注解

  ```java
  @Target(ElementType.METHOD)
  @Retention(RetentionPolicy.RUNTIME)
  public @interface OnClick {
      String value();
  }
  ```
- 定义OnClickProxyFactory来处理动态绑定方法的逻辑

  ```Java
  public class OnClickProxyFactory {

      /**
       * 处理OnClick注解
       *
       * @param target 包含OnClick注解的目标对象
       */
      public static void handleOnClickAnnotation(Object target) {
          try {
              Class<?> clsTarget = target.getClass();
              // 检索目标对象的所有方法, 如果含有OnClick注解, 则为注解中声明的属性对象动态添加AddActionListener方法
              for (Method method : clsTarget.getDeclaredMethods()) {
                  OnClick onClickAnnotation = method.getAnnotation(OnClick.class);
                  if (onClickAnnotation != null) {
                      Field field = clsTarget.getDeclaredField(onClickAnnotation.value());
                      field.setAccessible(true);
                      autoAddActionlistener(field.get(target), getActionListenerProxy(target, method));
                  }
              }
          } catch (Exception e) {
              e.printStackTrace();
          }
      }

      /**
       * 为目标对象添加addActionListener方法, 该方法的参数ActionListener为ActionListener代理对象
       * @param target 被添加ActionListener方法的事件源对象
       * @param actionListenerProxy ActionListener代理对象
       * @throws NoSuchMethodException
       * @throws InvocationTargetException
       * @throws IllegalAccessException
       */
      private static void autoAddActionlistener(Object target, Object actionListenerProxy) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
          // 获取JButton的addActionListener方法对象
          Method methodAddActionListener = target.getClass().getMethod("addActionListener", ActionListener.class);
          // 为JButton对象添加addActionListener方法
          methodAddActionListener.invoke(target, actionListenerProxy);
      }

      /**
       * 获取ActionListener的代理对象
       * @param target 指定的代理对象
       * @param targetMethod 指定的代理对象的方法
       * @return
       */
      public static Object getActionListenerProxy(final Object target, final Method targetMethod) {
          InvocationHandler handler = new InvocationHandler() {
              @Override
              public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                  return targetMethod.invoke(target);
              }
          };
          return Proxy.newProxyInstance(target.getClass().getClassLoader(), new Class[] {ActionListener.class}, handler);
      }
  }
  ```
- 在UI初始化时，调用注解的解析和动态处理

  ```Java
  public class UI extends JFrame{
      // 省略......
      public UI() {
        // 省略......

        // 通过OnClickProxyFactory工厂来处理OnClick注解
        OnClickProxyFactory.handleOnClickAnnotation(this);
    }
    
    @OnClick("btnBlue")
    public void setBlueBackground() {
        panel.setBackground(Color.BLUE);
    }

    @OnClick("btnRed")
    public void setRedBackground() {
        panel.setBackground(Color.RED);
    }

    @OnClick("btnSayHello")
    public void sayHello() {
        JOptionPane.showConfirmDialog(null, "Hello World!", "Tips", JOptionPane.YES_OPTION);
    }
  ```

具体的示例源码，移步 [Github-helloJavaSE](https://github.com/wangsheng/helloJavaSE)

## 参考

- [Java Annotation原理分析(一)](http://blog.csdn.net/blueheart20/article/details/18725801)
- [Java Annotation原理分析(二)](http://blog.csdn.net/blueheart20/article/details/18761847)
- [Java Annotation原理分析(三)](http://blog.csdn.net/blueheart20/article/details/18765891)
- [Java Annotation原理分析(四)](http://blog.csdn.net/blueheart20/article/details/18810693)
