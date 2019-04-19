# Datalog language learning.

***Follow '[LearnDatalogToday](http://www.learndatalogtoday.org/)' tutorial to practice.*** 

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



***Example:*** "Find all movies titles in the database"

```
[:find ?title
 :where
 [_ :movie/title ?title]]
```

| element                 | type    |
| ----------------------- | ------- |
| :find                   | keyword |
| ?title                  | symbol  |
| :where                  | keyword |
| [_ :movie/title ?title] | vector  |

***Result:***

![1](demo_images/1.png)

