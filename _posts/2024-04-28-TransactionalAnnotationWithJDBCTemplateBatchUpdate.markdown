---
layout: post
title:  "Batch update with @Transactional in Spring JDBC Template"
date:   2024-04-28 10:14:42 +0100
tag:    java
---

## Context

This is something which confused me when using spring's NamedParameterJdbcTemplate.

{% highlight ruby %}

@Transactional
public int[] insertAll (List<SaveThisObject> objsToInsert){
	SqlParameterSources parameterSources = SqlParameterSourceUtils.createBatch(objsToInsert.toArray());
	return this.jdbcTemplate.updateBatch(insertSql, parameterSources);

}

{% endhighlight %}

## Problem
With the above code I had observed that sometimes when the size of objsToInsert is in the order of many thousands, there was nothing inserted in the table.

## Analysis
Looking at the api implementation for NamedParameterJdcTemplate udpateBatch(..) used in the snippet reveals that 
the _entire list of objects is considered as a single batch_.
You could  overcome this limitation using a different batchUpdate API - the one that enables you to override BatchPreparedStatementSetter's getBatchSize 
api and chunk the list of inserts into smaller batches. A batch here is nothing but set of sql commands to be executed one for every object to be inserted
which is added to a single PreparedStatement object. So a single batch when executed sends this bunch of commands to the database server in a single go.
The recommended batch size is between 50 - 100. Small batches only enable smaller chunks to travel over the wire which are more reliable than 1 humungous dataset.
So you are trading off latency over reliability. The other important thing to note here ist that a batch has nothing to do with what gets committed. 
That is a commit doesnt happen per batch when you have @Transactional on the method. If one batch fails everything gets rolledback and nothing is inserted. 

## Solution
This a good use case where we need to use _Nested Transactions_ with spring jdbc.
The links below provide excellent explanations of how to go about doing that, many thanks to the authors of the links.
- http://mvpjava.com/spring-nested-transactions/
- https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth
