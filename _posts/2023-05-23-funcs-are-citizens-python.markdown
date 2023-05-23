---
layout: post
title:  "Functions First"
date:   2023-05-23 15:22:42 +0100
tag:    python
---
What makes development in python fast and fun is the idea that functions are first class citizens.  A python module/script can in fact begin with either calling or defining a function. This feels a bit awkward at first if  like me you have spent considerable time developing in an object oriented language. The thinking needs to be a bit more granular. Functions and its side-effects are all that matter, purer the better.

{% highlight ruby %}
Test.java
public class Test {

public static void main(String[] args) {

System.out.println("Hello World");

}

test.py
print("Hello World")
{% endhighlight %}

You see the difference! The smallest unit of work in python is a function in java its a class. Python does support object orientation but that's not the default paradigm.
