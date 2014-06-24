---
layout: post
title: 10个Java开发人员易犯的错误(译)
comments: true
---
下面总结了10个Java程序员最易犯的错误.

##1.Array与ArrayList转换

将一个数组array转换为ArrayList,程序猿通常这样做：
{% highlight java %}
List<String> list = Arrays.asList(arr);
{% endhighlight %}



Arrays.asList() 会返回一个Arrays的内部私有静态类的对象.
该内部类实现了List接口,而不是java.util.ArrayList.
java.util.Arrays.ArrayList类包含方法：set(), get(), contains(),但是不包含添加元素的方法,因此该list对象的定长的.
如果要创建一个真正的ArrayList,你需要以下操作：
{% highlight java %}
ArrayList<String> arrayList = new ArrayList<String>(Arrays.asList(arr));
{% endhighlight %}
ArrayList的构造函数可以接收Collection类型,Collection同样是java.util.Arrays.ArrayList的基类.

##2. 检测Array中是否包含某值

可以通过以下方式实现:
{% highlight java %}
Set<String> set = new HashSet<String>(Arrays.asList(arr));
return set.contains(targetValue);
{% endhighlight %}
以上代码是可用的, 但是没有必要先将List转化为Set,这样会带来额外的时间开销.
因此,可以简化为以下方式：
{% highlight java %}
Arrays.asList(arr).contains(targetValue);
{% endhighlight %}
或者
{% highlight java %}
for(String s: arr){
  if(s.equals(targetValue))
    return true;
}
return false;
{% endhighlight %}
相较于第二段代码,第一段有更好的可读性.

##3.通过循环在List中移除元素

以下代码为在循环迭代中移除元素:
{% highlight java %}
ArrayList<String> list = new ArrayList<String>(Arrays.asList("a", "b", "c", "d"));
for (int i = 0; i < list.size(); i++) {
  list.remove(i);
}
System.out.println(list);
{% endhighlight %}
输出为:
[b, d]
这个方法中有一个严重的问题.当元素被移除后,list的大小和索引会发生变化.因此,如果想通过遍历索引来删除多个集合中的元素,以上代码不会起到相应的作用.

你或许已经了解可以通过迭代器来删除元素,并且你也知道增强型for循环的工作原理就是迭代器.
但是实际上这是错误的.参考以下代码:
{% highlight java %}
ArrayList<String> list = new ArrayList<String>(Arrays.asList("a", "b", "c", "d"));
 
for (String s : list) {
  if (s.equals("a"))
    list.remove(s);
}
{% endhighlight %}
这段代码将会抛出ConcurrentModificationException.

替换为以下代码,则功能可用:
{% highlight java %}
ArrayList<String> list = new ArrayList<String>(Arrays.asList("a", "b", "c", "d"));
Iterator<String> iter = list.iterator();
while (iter.hasNext()) {
  String s = iter.next();
 
  if (s.equals("a")) {
    iter.remove();
  }
}
{% endhighlight %}
.next()的调用必须发生在.remove()之前. 在增强for循环中,编译器将会使.next()的调用发生在remove操作之后, 这将会引发ConcurrentModificationException. 如果有兴趣,可用参考ArrayList.iterator()的源码.

##4. Hashtable vs HashMap

By conventions in algorithm, Hashtable is the name of the data structure. But in Java, the data structure's name is HashMap. One of the key differences between Hashtable and HashMap is that Hashtable is synchronized. So very often you don't need Hashtable, instead HashMap should be used.

HashMap vs. TreeMap vs. Hashtable vs. LinkedHashMap
Top 10 questions about Map

##5. Use Raw Type of Collection

In Java, raw type and unbounded wildcard type are easy to mixed together. Take Set for example, Set is raw type, while Set<?> is unbounded wildcard type.

Consider the following code which uses a raw type List as a parameter:

{% highlight java %}
public static void add(List list, Object o){
  list.add(o);
}
public static void main(String[] args){
  List<String> list = new ArrayList<String>();
  add(list, 10);
  String s = list.get(0);
}
{% endhighlight %}
This code will throw an exception:

Exception in thread "main" java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
  at ...
Using raw type collection is dangerous as the raw type collections skip the generic type checking and not safe. There are huge differences between Set, Set<?>, and Set<Object>. Check out
Raw type vs. Unbounded wildcard and Type Erasure.

##6. Access Level

Very often developers use public for class field. It is easy to get the field value by directly referencing, but this is a very bad design. The rule of thumb is giving access level for members as low as possible.

public, default, protected, and private

##7. ArrayList vs. LinkedList

When developers do not know the difference between ArrayList and LinkedList, they often use ArrayList, because it looks familiar. However, there is a huge performance difference between them. In brief, LinkedList should be preferred if there are a large number of add/remove operations and there are not a lot of random access operations. Check out ArrayList vs. LinkedList to get more information about their performance if this is new to you.

##8. Mutable vs. Immutable

Immutable objects have many advantages such simplicity, safety, etc. But it requires a separate object for each distinct value, and too many objects might cause high cost of garbage collection. There should be a balance when choosing between mutable and immutable.

In general, mutable objects are used to avoid producing too many intermediate objects. One classic example is concatenating a large number of strings. If you use an immutable string, you would produce a lot of objects that are eligible for garbage collection immediately. This wastes time and energy on the CPU, using a mutable object the right solution (e.g. StringBuilder).

{% highlight java %}
String result="";
for(String s: arr){
  result = result + s;
}
{% endhighlight %}
There are other situations when mutable objects are desirable. For example passing mutable objects into methods lets you collect multiple results without jumping through too many syntactic hoops. Another example is sorting and filtering: of course, you could make a method that takes the original collection, and returns a sorted one, but that would become extremely wasteful for larger collections. (From dasblinkenlight's answer on Stack Overflow)

Why String is Immutable?

##9. Constructor of Super and Sub

This compilation error occurs because the default super constructor is undefined. In Java, if a class does not define a constructor, compiler will insert a default no-argument constructor for the class by default. If a constructor is defined in Super class, in this case Super(String s), compiler will not insert the default no-argument constructor. This is the situation for the Super class above.

The constructors of the Sub class, either with-argument or no-argument, will call the no-argument Super constructor. Since compiler tries to insert super() to the 2 constructors in the Sub class, but the Super's default constructor is not defined, compiler reports the error message.

To fix this problem, simply 1) add a Super() constructor to the Super class like

{% highlight java %}
public Super(){
    System.out.println("Super");
}
{% endhighlight %}
, or 2) remove the self-defined Super constructor, or 3) add super(value) to sub constructors.

Constructor of Super and Sub

##10. "" or Constructor?

String can be created by two ways:

{% highlight java %}
//1. use double quotes
String x = "abc";
//2. use constructor
String y = new String("abc");
{% endhighlight %}
What is the difference?

The following examples can provide a quick answer:

{% highlight java %}
String a = "abcd";
String b = "abcd";
System.out.println(a == b);  // True
System.out.println(a.equals(b)); // True
 
String c = new String("abcd");
String d = new String("abcd");
System.out.println(c == d);  // False
System.out.println(c.equals(d)); // True
{% endhighlight %}
For more details about how they are allocated in memory, check out Create Java String Using ” ” or Constructor?.

Future Work

The list is based on my analysis of a large number of open source projects on GitHub, Stack Overflow questions, and popular Google queries. There is no evaluation to prove that they are precisely the top 10, but definitely they are very common. Please leave your comment, if you don’t agree with any part. I would really appreciate it if you could point out some other mistakes that are more common.