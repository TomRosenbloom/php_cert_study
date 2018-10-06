# SQL

Aside: those Asdan guys had a thing about many-to-many relationships being bad and I never understood what they were on about and I still don't to be honest. Their thing was that you replace many-to-many with opposite one-to-manys, and they seemed to think this was challenging and revolutionary (?). As far as I can see, it is literally impossible to have a many-to-many relationship in a relational database, and *everyone* ***always*** does it as two one-to-manys via a bridging table (or pivot table, or join table, whatever you want to call it). So not clever at all, just standard practice. Use of a bridging table has the additional benefit that you can store extra information about the relationship. There are some controversies about the subtly different ways that you can implement it - for example it isn't strictly correct to have an additional id as a primary key, but is usually done that way as a programming convenience (for e.g. in a table called Authorship that has Author_id and Book_id as one-to-many rels to Author and Book tables, you also have an authorship Id column). So, other than that I still don't get what those guys were on about. There must have been something more to it than this right?

## MySQL syntax

I don't really need to go over the real basics of SQL, but a bit of syntax revision won't hurt, seeing as I rarely do this stuff by hand.

```sql
CREATE TABLE sales
(id int NOT NULL AUTO_INCREMENT,
 name varchar(6),
 sale_value float,
PRIMARY KEY (id))
```

In other flavours of SQL the primary key constraint is added with the column definition.

To add or alter a primary key:

```sql
ALTER TABLE sales
ADD PRIMARY KEY (id);

ALTER TABLE sales
ADD CONSTRAINT pk_sale PRIMARY KEY (id, name);
```

Note the second example adds a key composed from two columns. In phpMyAdmin this would be shown as an index, as well as the columns being shown as keys. Keys and indexes in MySQL are effectively synonymous.

Populate the table:

```sql
INSERT INTO sales
(name, sale_value) VALUES ('Jim', 2.5);

INSERT INTO sales (id, name, sale_value) VALUES 
(NULL, John, 3.5), 
(NULL, Jim, 1.5);
```



## Aggregating and grouping

```sql
SELECT name, SUM(sale_value)
FROM sales
GROUP BY name
```

Without the GROUP BY, we will get just a single value, the sum of sale_value for the entire table (and the last name in the table). With it, we get a separate sum for each person.

## OVER and window functions

'Window functions' such as OVER use aggregates e.g. SUM but without grouping i.e. returning a row for each table row, but with the aggregate columns repeated in each row.

Example from https://dev.mysql.com/doc/refman/8.0/en/window-functions-usage.html:

```sql
mysql> SELECT
year, country, product, profit,
SUM(profit) OVER() AS total_profit,
SUM(profit) OVER(PARTITION BY country) AS country_profit
FROM sales
ORDER BY country, year, product, profit;
+------+---------+------------+--------+--------------+----------------+
| year | country | product    | profit | total_profit | country_profit |
+------+---------+------------+--------+--------------+----------------+
| 2000 | Finland | Computer   |   1500 |         7535 |           1610 |
| 2000 | Finland | Phone      |    100 |         7535 |           1610 |
| 2001 | Finland | Phone      |     10 |         7535 |           1610 |
| 2000 | India   | Calculator |     75 |         7535 |           1350 |
| 2000 | India   | Calculator |     75 |         7535 |           1350 |
| 2000 | India   | Computer   |   1200 |         7535 |           1350 |
| 2000 | USA     | Calculator |     75 |         7535 |           4575 |
| 2000 | USA     | Computer   |   1500 |         7535 |           4575 |
| 2001 | USA     | Calculator |     50 |         7535 |           4575 |
| 2001 | USA     | Computer   |   1200 |         7535 |           4575 |
| 2001 | USA     | Computer   |   1500 |         7535 |           4575 |
| 2001 | USA     | TV         |    100 |         7535 |           4575 |
| 2001 | USA     | TV         |    150 |         7535 |           4575 |
+------+---------+------------+--------+--------------+----------------+
```

## Joins

![Image result for sql joins venn](http://i.imgur.com/hhRDO4d.png)



## Prepared statements

Using prepared statements has two purposes:

1. efficiency when executing the same query but with different params
2. security - provides some protection against SQL injection

## PDO

Stands for PHP Data Object. Abstracts data *access*, but isn't a full data abstraction layer like an ORM. So it has to be handed queries that are syntactically correct for the database you are using. 