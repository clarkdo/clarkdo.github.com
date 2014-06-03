---
layout: post
title: JavaScript对象模型-执行模型(转)
comments: true
--
简单数值类型: 有Undefined, Null, Boolean, Number和String。注意，描述中的英文单词在这里仅指数据类型的名称，并不特指JS的全局对象N an, Boolean, Number, String等，它们在概念上的区别是比较大的。<br>
对象: 一个无序属性的集合，这些属性的值为简单数值类型、对象或者函数。同上，这里的对象并不特指全局对象Object。<br>
函数: 函数是对象的一种，实现上内部属性[[Class]]值为"Function"，表明它是函数类型，除了对象的内部属性方法外，还有[[Construct]]、[[Call]]、[[Scope]]等内部属性。函数作为函数调用与构造器(使用new关键字创建实例对象)的处理机制不一样(Function对象除外)，内部方法[[Construct]]用于实现作为构造器的逻辑，方法[[Call]]实现作为函数调用的逻辑。同上，这里的函数并不特指全局对象Function。<br>
函数在JS这个Prototype语言中可以看作是面向对象语言的类，可以用它来构造对象实例。既然函数可以看作是类，所以每一个函数可以看作是一种扩展数据类型。<br>
<br>