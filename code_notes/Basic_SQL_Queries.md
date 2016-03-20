##`ADD COLUMN`

+ ALTER TABLE `table_name` ADD COLUMN `column_name` DATA_TYPE;

##`ORDER BY()`

**SYNTAX:**

```
SELECT column_name, column_name
FROM table_name
ORDER BY column_name ASC|DESC, column_name ASC|DESC;
```

`ORDER BY()` will automatically sort the returned values in ascending order. Use the `DESC` keyword, as above, to sort in descending order.

**Example:**

`SELECT * FROM cats ORDER BY(net_worth) DESC;`

This will return:

```
name             age         breed          net_worth 
---------------  ----------  -------------  ----------
Lil' Bub         2           Tortoiseshell  2000000   
Maru             3           Scottish Fold  1000000   
Grumpy Cat       4           Persian        181600    
Hana             1           Tabby          21800     
```
##`LIMIT`

**Example:**

```
SELECT * FROM cats ORDER BY(net_worth) DESC LIMIT 1;
```

Returns:

```
name             age         breed          net_worth 
---------------  ----------  -------------  ----------
Lil' Bub         2           Tortoiseshell  2000000   
```

The `LIMIT` keyword specifies how many of records that result from the query you'd like to actually return.

##`GROUP BY()`

The `GROUP BY()` keyword is very similar to `ORDER BY()`. The only difference is that `ORDER BY()` sorts the resulting data set of basic queries while `GROUP BY()` sorts the result sets of aggregate functions.

**SYNTAX**

```
SELECT column_name, aggregate_function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```

**Example:**

Let's calculate the sum of the net worth of all of the cats that belong to our second owner:

```
SELECT SUM(Cats.net_worth) 
FROM Owners 
INNER JOIN cats_owners 
ON Owners.id = cats_owners.owner_id 
JOIN Cats ON cats_owners.cat_id = Cats.id 
WHERE cats_owners.owner_id = 2;
```

This should return:

```
SUM(Cats.net_worth) 
--------------------
1181600 
```

In the above query, we use the `SUM(Cats.net_worth)` aggregator. `SUM` looks at the all of the values in the `net_worth` column of the Cats table (or whatever column you specify in parentheses) and takes the sum of the those values.

##`HAVING VS WHERE CLAUSE1`

Suppose we have a table called `employee_bonus` as shown below. Note that the table has multiple entries for employees Abigail and Matthew.

`employee_bonus`:

Employee | Bonus
---------|------
Matthew | 1000
Abigail | 2000
Matthew | 500
Tom	 | 700
Abigail | 1250

To calculate the total bonus that each employee received, we would write a SQL statement like this:

```
SELECT employee, sum(bonus) from employee_bonus 
group by employee;
```

This should return:

```
Employee	Bonus
Matthew	1500
Abigail	3250
Tom	700
```

Now, suppose we wanted to find the employees who received more than $1,000 in bonuses for the year of 2007. You might think that we could write a query like this:

```
BAD SQL:
select employee, sum(bonus) from employee_bonus 
group by employee where sum(bonus) > 1000;
```

Unfortunately the above will not work because the where clause doesn’t work with aggregates – like sum, avg, max, etc. What we need to use is the `HAVING` clause. The having clause was added to sql just so we could compare aggregates to other values – just how the ‘where’ clause can be used with non-aggregates. Now, the correct sql will look like this:

```
GOOD SQL:
select employee, sum(bonus) from employee_bonus 
group by employee having sum(bonus) > 1000;
```

**DIFFERENCE BETWEEN HAVING AND WHERE CLAUSE**

The difference between the having and where clause in sql is that the where clause can not be used with aggregates, but the having clause can. One way to think of it is that the having clause is an additional filter to the where clause.

##`INNER JOIN`

This is an inner join. We only want the students with a certain grade. You can imagine a Venn Diagram where one circle is "Grades" and another circle is "Students". We only want the overlapping (or "inner") parts of the two circles.

![inner_join](http://readme-pics.s3.amazonaws.com/Grade%20example%20Venn%20Diagram.png)

##`LEFT OUTER JOIN`

This is the most common outer join, and the one you'll use most often. This returns the normal inner join result and also returns all of the rows from the left-most (i.e. first mentioned) table.

![left_outer_join](http://readme-pics.s3.amazonaws.com/Payment%20example%20Venn%20Diagram.png)

##`RIGHT OUTER JOIN`

A Right Outer Join returns all the the data from the right circle, and it includes the overlapping information from the left circle.

![right_outer_join](http://readme-pics.s3.amazonaws.com/Right%20Outer%20Join%20Venn%20Diagram.png)

##`FULL OUTER JOIN`

A Full Outer Join returns all the data from all the tables, including the overlapping data.

![full_outer_join](http://readme-pics.s3.amazonaws.com/Full%20Outer%20Join%20Venn%20Diagram.png)