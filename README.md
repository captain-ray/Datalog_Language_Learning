# Datalog language learning.

***I follow '[LearnDatalogToday](http://www.learndatalogtoday.org/)' tutorial to practice.*** 

> **Learn Datalog Today** is an interactive tutorial designed to teach you the [Datomic](http://datomic.com/) dialect of [Datalog](http://en.wikipedia.org/wiki/Datalog). Datalog is a declarative **database query language** with roots in logic programming. Datalog has similar expressive power as [SQL](http://en.wikipedia.org/wiki/Sql).
>
> Datomic is a new database with an interesting and novel architecture, giving its users a unique set of features. You can read more about Datomic at [http://datomic.com](http://datomic.com/) and the architecture is described in some detail [in this InfoQ article](http://www.infoq.com/articles/Architecture-Datomic).



### 1.1 Extensible Data Notation

>In Datomic, a Datalog query is written in [extensible data notation (edn)](http://edn-format.org/). Edn is a data format similar to JSON, but it:
>
>- is extensible with user defined value types,
>- has more base types,
>- is a subset of [Clojure](http://clojure.org/) data.
>
>
>
>Edn consists of:
>
>- Numbers: `42`, `3.14159`
>- Strings: `"This is a string"`
>- Keywords: `:kw`, `:namespaced/keyword`, `:foo.bar/baz`
>- Symbols: `max`, `+`, `?title`
>- Vectors: `[1 2 3]` `[:find ?foo ...]`
>- Lists: `(3.14 :foo [:bar :baz])`, `(+ 1 2 3 4)`
>- Instants: `#inst "2013-02-26"`
>- .. and a few other things which we will not need in this tutorial.



***Practice:*** "Find all movies titles in the database"

```
[:find ?title
 :where
 [_ :movie/title ?title]]
```

| element                  | type    |
| ------------------------ | ------- |
| :find                    | keyword |
| ?title                   | symbol  |
| :where                   | keyword |
| [ _ :movie/title ?title] | vector  |

***Result:***

![1](demo_images/1.png)



### 1.2 Basic Queries

> You can think of the database as a flat **set of datoms** of the form:
>
> ```
> [<e-id>  <attribute>      <value>          <tx-id>]
> ...
> [ 167    :person/name     "James Cameron"    102  ]
> [ 234    :movie/title     "Die Hard"         102  ]
> [ 234    :movie/year      1987               102  ]
> [ 235    :movie/title     "Terminator"       102  ]
> [ 235    :movie/director  167                102  ]
> ...
> ```
>
> A query is represented as a vector starting with the keyword `:find` followed by one or more **pattern variables** (symbols starting with `?`, e.g. `?title`). After the find clause comes the `:where` clause which restricts the query to datoms that match the given **data patterns**.
>
> For example, this query finds all entity-ids that have the attribute `:person/name` with a value of `"Ridley Scott"`:
>
> ```
> [:find ?e
>  :where
>  [?e :person/name "Ridley Scott"]]
>  
>  //'_' can be used as a wildcard
>  
>  [:find ?e
>  :where
>  [?e :person/name "Ridley Scott" _]]
>  
>  //So, the above two queries are equivalent
> ```



***Practice1:*** Find the entity ids of movies made in 1987

```
[:find ?e
:where
[e? :movie/year 1987]]
```



***Practice2:*** Find the entity-id and titles of movies in the database

```
[:find ?e ?title
 :where
 [?e :movie/title ?title ]]
```



***Practice3:*** Find the name of all people in the database

```
[:find ?name
 :where
 [?p :person/name ?name]]
```

***Result3:***

![4](demo_images/4.png)



### 1.3 Data patterns

>In the previous chapter, we looked at **data patterns**, i.e., vectors after the `:where` clause, such as `[?e :movie/title "Commando"]`. There can be many data patterns in a `:where` clause:
>
>```
>[:find ?title
> :where
> [?e :movie/year 1987]
> [?e :movie/title ?title]]
>```
>
>he important thing to note here is that the pattern variable `?e` is used in both data patterns. When a pattern variable is used in multiple places, the query engine requires it to be bound to the same value in each place. Therefore, this query will only find movie titles for movies made in 1987.
>
>The order of the data patterns does not matter.



***Practice1:***  Find movie titles made in 1985

```
[:find ?title
 :where
 [?e :movie/year 1985]
 [?e :movie/title ?title]]
```



***Practice2:*** What year was "Alien" released?

```
[:find ?year
 :where
 [?m :movie/title "Alien"]
 [?m :movie/year ?year]]
```



***Practice3:*** Who directed RoboCop? You will need to use `[<movie-eid> :movie/director <person-eid>]` to find the director for a movie.

```
[:find ?name
 :where
 [?m :movie/title "RoboCop"]
 [?m :movie/director ?p]
 [?p :person/name ?name]]
```



***Practice4:*** Find directors who have directed Arnold Schwarzenegger in a movie.

```
[:find ?name
 :where
 [?p :person/name "Arnold Schwarzenegger"]
 [?m :movie/cast ?p]
 [?m :movie/director ?d]
 [?d :person/name ?name]]
```



### 1.4 Parameterized queries

> query with an input parameter for the actor
>
> ```
> [:find ?title
>  :in $ ?name
>  :where
>  [?p :person/name ?name]
>  [?m :movie/cast ?p]
>  [?m :movie/title ?title]]
> ```
>
> Tuples: A tuple input is written as e.g. `[?name ?age]` and can be used when you want to destructure an input. Let's say you have the vector `["James Cameron" "Arnold Schwarzenegger"]` and you want to use this as input to find all movies where these two people collaborated
>
> Collections: You can use collection destructuring to implement a kind of logical **or** in your query. Say you want to find all movies directed by either James Cameron **or** Ridley Scott
>
> Relations: a set of tuples - are the most interesting and powerful of input types, since you can join external relations with the datoms in your database.



***Practice1:*** Find movie title by year

```
[:find ?title
 :in $ ?year
 :where
 [?m :movie/year ?year]
 [?m :movie/title ?title]]
```

***Result1:***

![5](demo_images/5.png)

****



***Practice2:*** Given a list of movie titles, find the title and the year that movie was released.

```
[:find ?title ?year
 :in $ [?title ...]
 :where
 [?m :movie/title ?title]
 [?m :movie/year ?year]]
```



***Practice3:*** Find all movie `?title`s where the `?actor` and the `?director` has worked together

```
[:find ?title
 :in $ ?actor ?director
 :where
 [?a :person/name ?actor]
 [?d :person/name ?director]
 [?m :movie/cast ?a]
 [?m :movie/director ?d]
 [?m :movie/title ?title]]
```

process:

1. input actor and director seperately
2. find actorID and directorID based on name
3. find movieID related to the actorID and directorID
4. find movie title based on movieID



***Practice4:*** Write a query that, given an actor name and a relation with movie-title/rating, finds the movie titles and corresponding rating for which that actor was a cast member.

```
[:find ?title ?rating
 :in $ ?actor [[?title ?rating]]
 :where
 [?a :person/name ?actor]
 [?m :movie/cast ?a]
 [?m :movie/title ?title]]
```

process:

1. find actorID based on actor name
2. find movieID based on actorID
3. find movieID based on title



