---
layout: post
title: Scala文件操作
category: 技术
tags: blog
---

在Scala中文件读取操作，我们通常调用object类scala.io.Source提供的API接口来读取文件。API中提供了丰富的接口，基本能满足各种来源的数据读取。  

{% highlight scala %}
object Source extends scala.AnyRef {
  val DefaultBufSize : scala.Int = { /* compiled code */ }
  def stdin : scala.io.BufferedSource = { /* compiled code */ }
  def fromIterable(iterable : scala.Iterable[scala.Char]) : scala.io.Source = { /* compiled code */ }
  def fromChar(c : scala.Char) : scala.io.Source = { /* compiled code */ }
  def fromChars(chars : scala.Array[scala.Char]) : scala.io.Source = { /* compiled code */ }
  def fromString(s : scala.Predef.String) : scala.io.Source = { /* compiled code */ }
  def fromFile(name : scala.Predef.String)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(name : scala.Predef.String, enc : scala.Predef.String) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(uri : java.net.URI)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(uri : java.net.URI, enc : scala.Predef.String) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(file : java.io.File)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(file : java.io.File, enc : scala.Predef.String) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(file : java.io.File, enc : scala.Predef.String, bufferSize : scala.Int) : scala.io.BufferedSource = { /* compiled code */ }
  def fromFile(file : java.io.File, bufferSize : scala.Int)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromBytes(bytes : scala.Array[scala.Byte])(implicit codec : scala.io.Codec) : scala.io.Source = { /* compiled code */ }
  def fromBytes(bytes : scala.Array[scala.Byte], enc : scala.Predef.String) : scala.io.Source = { /* compiled code */ }
  def fromRawBytes(bytes : scala.Array[scala.Byte]) : scala.io.Source = { /* compiled code */ }
  def fromURI(uri : java.net.URI)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromURL(s : scala.Predef.String, enc : scala.Predef.String) : scala.io.BufferedSource = { /* compiled code */ }
  def fromURL(s : scala.Predef.String)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromURL(url : java.net.URL, enc : scala.Predef.String) : scala.io.BufferedSource = { /* compiled code */ }
  def fromURL(url : java.net.URL)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def createBufferedSource(inputStream : java.io.InputStream, bufferSize : scala.Int = { /* compiled code */ }, reset : scala.Function0[scala.io.Source] = { /* compiled code */ }, close : scala.Function0[scala.Unit] = { /* compiled code */ })(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
  def fromInputStream(is : java.io.InputStream, enc : scala.Predef.String) : scala.io.BufferedSource = { /* compiled code */ }
  def fromInputStream(is : java.io.InputStream)(implicit codec : scala.io.Codec) : scala.io.BufferedSource = { /* compiled code */ }
}
{% endhighlight %}

多个接口中用到了隐式参数，实现默认文件编码格式的载入。用Scala的这个API读取文件最大的好处我认为是接口对系统资源操作进行了封装，可以防止资源不被安全释放(close)，其次是调用代码不用考虑资源来自何处，以及如何归还，只要专注如何去使用资源。今天才发现，我们经常用的这种封装方式叫做借贷模式（loan pattern），又涨姿势了。

不过我发现虽然API虽然可以防止资源不被安全释放，不显式地`close()`资源不会又问题，但是资源不是立刻被释放掉，如果需要马上再次获取该资源，有可能会异常，因此，在代码里最好还是显式地调用一下`close()`函数。

`Source.fromFile().getLines()`返回的是Iterator迭代器，遍历过的行，数据会丢失，如果需要重复使用，必须在内存中通过赋值保存好l。或者直接将Iterator转化为`List`或`Array`，这样就可以当作集合来做进一步处理了，但是代价就是需要需要消耗内存来保存文件中的所有数据行。这个可根据实际情况灵活应对，如果是小文件，直接转化Scala集合是及其方便的，且代码简洁，不需要用`while`或`for`循环去遍历迭代器；如果是大文件，避免使用集合操作，以免占用太多内存甚至内存溢出。 

对于写操作，Java中的写操作的接口都可以调用，用法完全一样。          




