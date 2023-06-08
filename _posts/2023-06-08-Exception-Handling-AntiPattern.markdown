---
layout: post
title:  "Catch, Log, Rethrow, Anti Pattern"
date:   2023-06-08 21:30:42 +0100
tag:    java
---
I was reviewing quite a bit code this week. There was One thing that stood out like a sore thumb at many places in the codebase.
The catch, log and rethrow pattern. It looked something like this:
{% highlight ruby %}
public class MyBrilliantProcessor {

  ....

 void doSomethingInteresting(){
 	try{    
           someInterestingFunc();
       }       
        catch(Exception ex){
          log.error("The Exception is %s", ex);
          throw ex;
        }
  }
  
 void someInterestingFunc(){
	try{
	    callSomeOtherFunc();
	}
	catch(Exception ex){
	   log.error("The Exception is %s", ex);
	   throw ex;
	}
  }	
}

{% end highlight %}

This is a code smell that we should avoid. 
Why? For one it doesn't seem that the exception is really handled. It is not doing anything meaningful (other than logging ofcourse) from a business logic perspective and reveals a knowledge gap of business process. A good question to ask is are we catching *Contingent Exception* or a *Fault*?
Second, a single exception gets logged multiple times which adds to the pain of debugging by making the logs unnecessarily cluttered. It also makes the code verbose.

##How to avoid this
Given that we do need to log the exception, the best strategy would be to log it at a single location where the exception is *actually* handled. For e.g. it could be in the function that acts like a template which directs the sequence of execution by calling other private functions. There we could also just wrap the exception into something domain specific and throw that.

{% highlight ruby %}
public void iJustDelegate(){
  try{
	callFunc1();
    	callFunc2();
        callFunc3();
}
//it would be great to catch the specific exception.
catch(Exception ex){
  throw new MyDomainException(ex);
}

private callFunc1(){}

private callFunc2(){}

private callFunc3(){}

}

{% end highlight %}
