---
layout: post
title:  "Working With SQL: Deriving New Data From Existing Columns"
date:   2015-10-16
categories: sql
tags: union case
---

If you spend your time working on web applications, it's almost inevitable that you will at some point find yourself working with a relational database. And even if that database is ultimately [a peripheral detail of your application][no_db], it's one with which you'll want to have a fairly intimate familiarity.

What do I mean by intimate familiarity? An ability to interact with that database comfortably, without the aid of an ORM. Putting aside questions as to whether [ORM is an anti-pattern][orm_antip] (or [the Vietnam of computer science][orm_vietnam]), there will almost certainly be situations in which understanding the underlying syntax of the database will make your life easier.

In this post, we'll get started with nurturing that sort of familiarity by discussing how to accomplish a simple task in PostgreSQL: deriving new data from the combination and/or evaluation of fields in a database.

**Combination: `UNION`**

Accessing data from a table's columns is fairly straightforward:

{% highlight sql %}
-- one column from a table
SELECT one_column FROM a_table;

-- multiple columns from a table
SELECT one_column, another_column FROM a_table;

-- all columns from a table
SELECT * FROM a_table;
{% endhighlight %}

These queries allow us access data that's either identical to or a subset of existing columns. But what if we want our query's results to *combine* data from several columns?

For example, imagine that you have a table `people` that stores the name, favorite actor, and favorite actress of several individuals.

id | name  | favorite_actor    | favorite_actress
:--|:------|:------------------|:-----------------
1  | Bob   | Brad Pitt         | Angelina Jolie
2  | Jane  | Bradley Cooper    | Jennifer Lawrence
3  | Sally | Leonardo DiCaprio | Kate Winslet

Now imagine that we want to extract the favorite actors and actresses into a new column representing all movie stars, without respect for gender.

movie_star        |
:-----------------|
Brad Pitt         |
Angeline Jolie    |
Bradley Cooper    |
Jennifer Lawrence |
Leonardo DiCaprio |
Kate Winslet      |

The simple queries listed at the top of this section won't work, since they return columns as they are represented in our database.

To query for a combination of data from several columns, we can take advantage of the `UNION` operation.

{% highlight sql %}
SELECT favorite_actor AS movie_star
	FROM people
UNION
SELECT favorite_actress
	FROM people;
{% endhighlight %}

The `UNION` operation lets us combine the results of several queries, allowing us to aggregate data both within and across tables.

**Derivation: `CASE`**

In the case of a `UNION` operation, our query returns results with a new column header, but merely a combination of contents - they all correspond to existing fields in our database.

If we want to query for results that yield both a new header and new contents, we can take advantage of the `CASE` operation.

For example, still working with our `people` table, say  we want to derive a column that tells us whether the person in question loves *Titanic*.

We can't really be sure that any of them don't love *Titanic*, but we feel comfortable predicting that they do love the film if their favorite actor is Leonardo DiCaprio and their favorite actress is Kate Winslet.

Using that criteria, we can put together our query like so:

{% highlight sql %}
SELECT name,
	CASE WHEN (favorite_actor = 'Leonardo DiCaprio' and favorite_actress = 'Kate Winslet') THEN 'Yes'
		ELSE 'Unsure'
	END AS loves_titanic
FROM people;
{% endhighlight %}

Since Sally was the only person in our table that matched the criteria, she will be the only person designated with a 'Yes' in our new column.

 name  | loves_titanic
:------|:---------------
 Bob   | Unsure
 Jane  | Unsure
 Sally | Yes

 Here, our query returns results without a 1-to-1 mapping to our database. By including a `CASE` operation that behaves differently depending on the contents of existing fields in our database, we're able to derive new information.

**Conclusion**

We haven't come close to exhausting the range of techniques you can use to derive new and sophisticated information from tables in a database, much less other information that you'll want to possess to accomplish other tasks, but we have covered some important ground.

If you're in an environment where you're likely to find yourself working with a relational database, the `UNION` and `CASE` operations are two techniques with which you'll want to be familiar.

[no_db]: https://blog.8thlight.com/uncle-bob/2012/05/15/NODB.html
[orm_antip]: http://seldo.com/weblog/2011/08/11/orm_is_an_antipattern
[orm_vietnam]: http://blogs.tedneward.com/2006/06/26/The+Vietnam+Of+Computer+Science.aspx
