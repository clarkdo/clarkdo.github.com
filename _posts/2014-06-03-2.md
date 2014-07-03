---
layout: post
title: How to Check if an Array Contains a Value in Java Efficiently?
comments: true
---
<div class="message">
How to check if an array (unsorted) contains a certain value? This is a very useful and frequently used operation in Java. It is also a top voted question on Stack Overflow. As shown in top voted answers, checking if an array contains a certain value can be done in several different ways, but the time complexity could be very different. In the following I will show the time each method takes.
</div>
###1. Four Different Ways to Check If an Array Contains a Value
1.Using List:
{% highlight js %}
public static boolean useList(String[] arr, String targetValue) {
    return Arrays.asList(arr).contains(targetValue);
}
{% endhighlight %}



2.Using Set:
{% highlight js %}
public static boolean useSet(String[] arr, String targetValue) {
    Set<String> set = new HashSet<String>(Arrays.asList(arr));
    return set.contains(targetValue);
}
{% endhighlight %}

3.Using a simple loop:
{% highlight js %}
public static boolean useLoop(String[] arr, String targetValue) {
    for(String s: arr){
        if(s.equals(targetValue))
            return true;
    }
    return false;
}
{% endhighlight %}

4.Using Arrays.binarySearch():
The code below is wrong, it is listed here for completeness. 
binarySearch() can ONLY be used on sorted arrays. You will the result is weird below.
{% highlight js %}
public static boolean useArraysBinarySearch(String[] arr, String targetValue) { 
    int a =  Arrays.binarySearch(arr, targetValue);
    if(a > 0)
        return true;
    else
        return false;
}
{% endhighlight %}