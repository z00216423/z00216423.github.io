---
layout: post
title: Java类型删除
category: 技术
tags: blog
---

前两天跟任航聊起Java中的类型删除概念。之前对Java的范型的具体实现没有关注过，第一次听到这么个概念。他是通过Java范型代码编译成字节码后再反编译，结果发现范型中的类型就消失。很有意思的一个现象，这么一说我还真是发现以前看到很多反编译的代码里都是`List`，而不是`List[T]`。于是，小研究了一下。

---

### 编译器对范型的处理
类型擦除涉及的是编译层面，我们来看一下编译都是怎么处理范型的。一般来说，编译器处理泛型有两种方式：
 
 - Code specialization（暂且翻译为代码特例化），即编译在实例化一个泛型类或泛型方法时都产生一份新的可执行代码，每个具体类型对应一份代码。
 - Code sharing（代码共享），即编译器对所有泛型类只生成唯一的一份可执行代码，该泛型类的所有具体类型都映射到这份目标代码上，在需要的时候执行类型检查和类型转换。

 C++的模版就是典型的Code specialization实现。这种方式对范型的处理简单而有效，但是明显的缺陷在于占用空间，造成代码膨胀。而Java采用的则是Code sharing， 估计就为了避免这个缺陷吧。一个范型只会生成一份字节码，所有的具体类型都会映射到这份字节码上，这个映射的过程就是所谓的类型擦除（type erasue）。

 Java的类型擦除的实现又分为两个步骤：

 - 替换所有范型类型为最左边界（Object类） 
 - 删除所有参数类型

 那么问题来，如果具体类型被删除了，编译后又怎么区分呢？答案是，编译后，JVM已经傻傻分不清楚了。所以对于方法重载，如果参数列表都一样，只是范型中的具体类型不一样的话，编译器会认为这两个方法签名是一样的，从而报错。我简单尝试写了一下： 

{% highlight java linenos %}
public class TestTypeErasue {

        public void test(List<String> ls){
            System.out.println("Sting");
        }
        public void test(List<Integer> li){
            System.out.println("Integer");
        }

}
{% endhighlight %}

 IDE报错提示这两个方法具有相同的类型擦除。`List<String>`和`List<Integer>`， 编译后，再JVM看来是一样的东西， 这是Java范型实现的一个弊端。这又涉及到一个类型与类的概念了。`List<String>`和`List<Integer>`是同一个类，但是是不同的类型。从这个例子可以看出，类与类型的关系是： **类相同，类型不一定相同；类型相同，肯定是同一个类。**

 ### Scala中的范型实现
 先占个坑，后面再补上