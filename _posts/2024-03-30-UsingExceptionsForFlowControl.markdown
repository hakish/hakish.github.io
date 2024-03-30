---
layout: post
title:  "Using Exceptions for Flow Control - Anti Pattern?"
date:   2024-03-30 20:14:42 +0100
tag:    java
---
I had the below code but a colleague pointed out in a code review that using exception to control the flow is a bad idea and perhaps even an anti pattern. 
I had to go back to the drawing and do some reading. And as it turns out this was a good advice (so plus one for code reviews). For one this is covered in
*Effective Java - 3rd* which explicitly mentions this "Item 69 - Use Exceptions only for exceptional conditions".

{% highlight ruby %}

public Object get(String key){
	try {
		return cache.get(key);
	}catch(KeyNotFoundException ex){
		Object value = fetchFromDB(key);
		cache.put(key, value);
		return value
	}

}

{% endhighlight  %}
It points to the fact that placing code in try - catch block prevents certain compiler optimizations which makes it a lot slower compared to the standard way.
Secondly, it also violates the principle of least suprise i.e. the api should behave in a way that is very natural for the user and meets his expectations.


## How to avoid this
Avoid throwing the exception if its the more frequent use case. In the above from java 8 onwards the api could accept a supplier that will be invoked in the exceptional case. In this specific example looking up the database if a key is not found in cache is a pretty common pattern.

{% highlight ruby %}

public Object get(String key, Supplier func){
	Object value = cache.get(key);
	if (value == null){
		value = func.get();
		cache.put(key, value);
	}
	return value;
}

{% endhighlight  %}

