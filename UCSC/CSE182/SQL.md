**History**
Two different query languages for the relational data model
- relational algebra: queries are a sequence of operations on relations (procedural language)
- relational calculus: queries are formulas of first order logic (declarative language)
Codd's Theorem: the two languages have the same expressive power
also note: declarative states what is the expected outcome, procedural states how the outcome is to be obtained

**SQL**
Structured Query Language (SQL) is the principal language used to describe and manipulate data stored in relational database systems.
- Somewhat declarative but not fully
- not the same as Codd's relational algebra or relational calculus

SQL is a mix of two sub-languages:
- Data Definition Language (DDL)
	- declares database schema
	- create, delete, modify definition of tables and views
- Data Manipulation Language (DML)
	-  insert, delete, modify rows and query data
	- used to ask questions abt the database and modify it like SELECT

**SQL Data Types**
- INT or INTEGER
	- int in C
- SMALLINT
	- short in C
- FLOAT and REAL
- DOUBLE PREICSION
	- double in c
- DECIMAL(n, d), NUMERIC(n, d)
	- n decimal digits d of them to the right of the decimal
- BOOLEAN or BOOL
	- TRUE, FALSE, UNKNOWN
- CHAR(n)
	- fixed length string of up to n characters
	- blank padded on the right to be exactly n chars (what does that mean kobe bryant ?)
- VARCHAR(n)
	- string of up to n chars not blank padded
- BIT(n)
- BIT VARYING(n)
- DATE, TIME, TIMESTAMP, INTERVAL
	- separate data types
	- constants are char strings of a specific form
- NULL
	- represents unknown values for any data type

Note: In SQL, always use a semicolon to end statements!

## SQL Actions (Data Definition Language Portion)
**Creating a Table**
Note: when creating tables, in PostgreSQL, all the table names will be converted to lowercase (and that's okay, doesn't matter)
```SQL
CREATE TABLE Movies {
	movieTitle CHAR(100),
	movieYear INT,
	length INT,
	genre CHAR(10),
	studioName CHAR(30),
	producerC# INT,
};

CREATE TABLE MovieStar {
	starName CHAR(50),
	address VARCHAR(200),
	gender CHAR(1),
	birthDate DATE,
};
```

**Deleting a Table**
In general, to delete stuff use the "DROP" keyword
```SQL
DROP TABLE Movies;
```

**Modifying a Table**
In general, use the "alyer" keyword
```SQL
# adding a new column to the table
ALTER TABLE MovieStar ADD phone CHAR(16);

# dropping a column from the table
ALTER TABLE MovieStar DROP birthdate;
```

**Default Values**
If we enter a row to an existing table and it's missing an attribute, it will take on the value NULL for that column, unless we specify a default value to fallback to. To do this, use the "DEFAULT" keyword followed by the default value. So for example,
```SQL
ALTER TABLE MovieStar ADD phone CHAR(16) DEFAULT "111-111-1111";
```

**Declaring Keys**
To declare an attribute to be a key, use the "PRIMARY KEY" keyword
```SQL
CREATE TABLE MovieStar {
	starName CHAR(50) PRIMARY KEY, # write next to attribute
	address VARCHAR(200),
	gender CHAR(1),
	birthDate DATE,
};

# you can also write it like this
CREATE TABLE MovieStar {
	starName CHAR(50),
	address VARCHAR(200),
	gender CHAR(1),
	birthDate DATE,
	PRIMARY KEY (starName) # write at the bottom of table
};

# if we have a key that has multiple attributes, we can write it as so
CREATE TABLE Movies {
	movieTitle CHAR(100),
	movieYear INT,
	length INT,
	genre CHAR(10),
	studioName CHAR(30),
	producerC# INT,
	PRIMARY KEY (movieTitle, movieYear) # write at bottom of table inside the parentheses
};
```

**Two Types of Keys in SQL**
- Primary key (PK)
	- does not allow NULL values
	- at most 1 in a table
- Unique key
	- basically all values in this column are also unique, but only the rows with non-null unique keys serve as an identifier for the row, in contrast to the primary key where ALL the rows are an identifier because there's no NULL values for PKs
	- allows NULL values
	- can be multiple in a table

**Unique Keys in SQL**
Use the "UNIQUE" keyword
```SQL
CREATE TABLE MovieStar {
	starName CHAR(50),
	address VARCHAR(200),
	gender CHAR(1),
	birthDate DATE,
	# to-do fix this in the slides is it unique or unique key ?
	UNIQUE (birthDate) # write at the bottom of table
};
```

**Making a column have non-null values**
Use the "NOT NULL" keyword next to the attribute
```SQL
birthdate DATE NOT NULL,
```

**Foreign Key**
A foreign key is NOT a key, but rather a constraint on the table that the foreign key attribute must match the value of a key in another table.

If the fields are named differently in each table but correspond to the same value, you have to specify the name of the field you want to link the foreign key to like in the Studio table. If the fields are named the same, you don't have to like in the Movies table
```SQL
CREATE TABLE MovieExec {
	execName CHAR(30),
	address VARCHAR(30),
	cert# INT PRIMARY KEY,
	netWorth INTEGER,
}

# fill in the value of presC# with the cert# key in MovieExec
CREATE TABLE Studio {
	studioName CHAR(30) PRIMARY KEY,
	address VARCHAR(255),
	presC# INT,
	FOREIGN KEY (presC#) REFRENCES MovieExec(cert#)
}

# in the movies table we also reference the studioname via a foreign key
CREATE TABLE Movies {
	studioName CHAR(30)
	FOREIGN KEY (studioName) REFERENCES Studio
}

"""
In this table, every foreign key like (movieTitle, movieYear) must be the primary key of their referenced table (so ig their referenced table like MovieStar can't have a two attribute PK but only one attribute since its FK is starName)

Also, since the StarsIn table has a 3 attribute PK and two FKs, each FK can have infinite/n entries in the StarsIn table since you can have duplicates of two fields, but not all three since the PK of StarsIn is three elements
"""
CREATE TABLE StarsIn {
	movieTitle CHAR(100),
	movieYear INT,
	starName CHAR(30),
	PRIMARY KEY (movieTitle, movieYear, starName),
	FOREIGN KEY (movieTitle, movieYear) REFERENCES Movies,
	FOREIGN KEY (starName) REFERENCES MovieStar
};
```

**PostgreSQL Commands**
- \\|
	- list the databases
- \\dt
	- list tables of a database
- \d table_name
	- list cols for a specific table name


## SQL Actions (Data Manipulation Language Portion)

Keywords:
- SELECT (columns)
	- choose which cols you want data from
	- you can also use the \* to select all columns (so the entire table)
- FROM (table)
	- choose which table you want to select data frrom
- WHERE (condition) 
	- constraint used to select specific data
- AND
	- joining conditions together

**SQL Query**
![[Pasted image 20250418171216.png]]
The \[DISTINCT] is optional and will be covered below.

Example: Selecting all the movies made by Disney and in 1990
```SQL
SELECT * 
FROM Movies 
WHERE studioName = 'Disney' AND movieYear = 1990;
```
In the example above, we use * as a wildcard for all attributes of relations in the FROM clause.

Behind the scenes, this is represented as something like
```python
for row in Movies:
	# filtering step
	if row.year = 1990 and row.studio = "Disney":
		return row
```

Example #2: Selecting all movies
`SELECT * FROM Movies;` is equivalent to `SELECT * FROM Movies WHERE TRUE;`

<u>Projection Query:</u> Only a subset of attributes from the relations in the FROM clause is selected
`SELECT movieTitle, movieYear FROM Movies;`

In a projection query, the filtering happens first (so narrowing down rows based on a potential WHERE clause), and then the projection of selecting a subset of attributes from the rows happens

<u>Distinct Queries</u>: Sometimes, your projection queries can have duplicate rows. For example, `SELECT movieYear FROM Movies` can have multiple rows of just 1990 as the only attribute since that's what we select for. Sure they're different Movies, but since we stripped away the other attributes like movieName, they're essentially the same/duplicate rows. 

So what do you do in this case?

Using the keyword DISTINCT, we can make it so that we only get one row back and thus avoid duplicates. So, `SELECT DISTINCT movieYear FROM Movies` would return only one row with the movieYear 1990 even if in the table there are many rows with a movieYear value of 1990.

The usage of the DISTINCT keyword and having row uniqueness is called <u>set semantics</u>, while the result of having duplicate rows from a projection query is called multiset/bag semantics.

**Bags (or Multisets) vs. Sets**
- Sets have distinct elements
- Bag or multiset may have repeated elements
	- Notation (double curly brackets): {{2, 2, 4, 6, 2, 2}}, can also be written as {{2\[3], 4\[1], 6\[1]}} so format is **element\[number of elements in multiset/bag]**
- Order among elements for both set and bag is not important

**Aliasing Attributes**
Alias allow you to rename the attributes of a result (not the attribute in the table itself, but in the result view of a query). 

To declare an alias, you use the AS operator followed by the new name/alias: `SELECT movieTitle AS name, length AS duration FROM Movies` which gives you a view where the two attributes movieTitle and length are now renamed into name and duration. 

**NOTE:** The keyword AS is optional for declaring alias **in the SELECT clause (for the FROM or JOIN clauses behavior varies)**. The following query is the same as the one above: `SELECT movieTitle name, length duration`

Also, be careful when using alias. For reserved keywords like order or group, you might have to quote the alias like "order" so SQL doesn't think you're actually using the order operator.

**Expressions in the SELECT Clause**
Expressions are allowed in the SELECT clause like `SELECT movieTitle AS name, length * 60 AS durationInSeconds FROM Movies;` and notice how we multiply the value of the length by 60 to create the renamed duration in seconds attribute

**Constants in the Result**
You can include constant fixed values directly in the output of our SQL SELECT statement that will have the same value for every row.

So for instance, we can have `SELECT movieTitle AS name, length * 60 AS durationInSeconds, 'seconds' AS inSeconds FROM Movies;` which displays a third constant value attribute for all rows with the value "seconds".
![[Pasted image 20250419015518.png]]

**WHERE Conditions**
In general, the WHERE clause consists of a boolean
combination of conditions using logical connectives where each condition is of the form: (expression) (operator) (expression) where expression is a column name, constant, or arithmetic/string expression while operator is a comparison operator.
- Comparison operators: =, <> (same thing as != and you can use != too), <, >, <=, >=, LIKE
- Logical connectives: AND, OR, NOT
- Arithmetic expressions: +, -, \* /, etc.
Example: `WHERE ( (length/60) > 2 OR (length<100) ) AND movieYear > 2000`

**String Comparisons**
Strings are compared in lexicographical order
- 'Pretty' < 'Pretty Woman'
- 'butterfingers' < 'butterfly'

**Pattern Matching in SQL**
We use LIKE as an operator for pattern matching/wildcards. The general format is `s LIKE p` or `s NOT LIKE p` where s is a string and p is a pattern.

Patterns:
- '%' is for 0 or more arbitrary chars
	- so, `SELECT * FROM Movies WHERE movieTitle LIKE 'Jurassic%'` selects movieTitles with Jurassic as the first part of the title
- '\_' is for exactly one char
	- 'Juras_ic' takes in any string that matches that pattern so like 'Jurassic' or 'Jurasxic'

Pattern matching for quotes:
We use two quotes to represent one quote in a pattern
- To match Monster's Inc, we do 'Monster''s Inc'
- So a single quote is '''' (two outside ' to encase the string, then two inner '' to represent a single quote)
- Similarly, if we want a double quote we use '' twice inside the string literal, so '''''' where the outside two ' are for encasing the string, and the 4 inner ' represent two single quotes

**Escape Characters**
We define an escape character with the ESCAPE operator and use that to represent symbols like % or _  which are typically reserved pattern matching symbols.

Example:
`WHERE movieTitle LIKE '!%%!_' ESCAPE '!'`
In this example, we use ! as our escape char but it can be any other character too. Notice how '!%%!\_' represents first a literal % char since we escape it with !%, then 0 or any values with the actual pattern matching % symbol, then a literal \_ char since we also escape it with !\_, and of course we define the usage of ! as an escape using the ESCAPE operator on the right.

Question: Would the above condition match the following strings?
- 'ABC%D' 
	- No
- '%ABC' 
	- No
- '%ABC_'
	- Yes
- 'ABCD' 
	- No
- '%\_'
	- Yes

**Dates and Times**
When referring to dates and times we have different choices: DATE, TIME, TIMESTAMP, and INTERVAL. 

These are all separate data types each with character strings of a specific form
![[Pasted image 20250419022136.png]]
They can be compared using ordinary comparison operators: `WHERE ReleaseDate <= DATE '2020-06-19'`. 

In PostgreSQL if myTimestamp is a TIMESTAMP value, then DATE(myTimestamp) is the DATE value for that TIMESTAMP

**SQL's Three-Valued Logic**
The three boolean states are TRUE, FALSE, and UNKNOWN, so if an attribute is NULL and it gets used in a comparison like a null major and having "major = 'Computer Science'", this evaluates to UNKNOWN
![[Pasted image 20250419022513.png]]
Notice how in the OR column even if the value of one attribute is unknown, as long as the other one is TRUE it's still TRUE since OR just requires one TRUE. However in the OR if one of the attributes is UNKNOWN and the other is FALSE, it evaluates to UNKNOWN.

If a condition evaluates to UNKNOWN, the tuple/current row <u>will not</u> be returned in the result view.

Note: UNKNOWN is not the same as FALSE. If this were the case you would have weird false positives for things like `NOT ( major = 'Computer Science' AND avgGPA > 3.00 )`

**NULL Interactions**
If you use comparisons on NULL values, they will result in UNKNOWN like if salary is NULL and you try to do `salary = NULL` or `salary <> NULL`.

However, if you use `IS NULL` or `IS NOT NULL` you get actual TRUE/FALSE values depending on if the salary is/isn't NULL

**NULL vs. UNKNOWN vs. Empty Set**
- attributes can have NULL values
- conditions can have UNKNOWN values
- query results can be the empty set
	- a table can also have no tuples in it

**Ordering Results**
We use the `ORDER BY` keyword to present results in a sorted order, with the results by default being presented in an ascending order

Format: `ORDER BY (attribute) (optional - ASC/DESC)`
- ASC/DESC for ascending/descending order

Example: `SELECT * FROM Movies WHERE studioName = 'Disney' AND movieYear = 1990 ORDER BY length, movieTitle;`
- Priority for ordering is given to attribute order. So ascending order of length (shortest length first) is prioritized, then if there's a tie we use ascending title value

You can also order by expressions: `ORDER BY Quantity * Price DESC`
- NULL also works for order by, treat it as the <u>smallest possible value</u> in this course but it depends on implementation usually

**SQL Query Conclusion**
Piecing all of this together, this is the final format of a SQL Query:
![[Pasted image 20250419024804.png]]

**Selecting from Multiple Relations**
When you select from multiple relations, the amount of attributes you can select from also extends to the attributes in those relations:
![[Pasted image 20250419032726.png]]

But what happens if two attributes each in different relations have the same name like movieTitle in the StarsIn and Movies relations?

This is where we have to specify the relation, so we do something like Movies.movieTitle to select the attribute, otherwise we get undefined behavior.

Example:
`SELECT * FROM Movies, StarsIn WHERE Movies.movieTitle = 'Pretty Woman' AND movieYear <= 1995;`

**Tuple Variables**
You can set variables that bind to a tuple (row) in a relation. For example, in `SELECT * FROM Movies m, StarsIn s WHERE m.movieTitle = s.movieTitle;`, m and s are both variables that bind to tuples in the Movies and StarsIn relation respectively.

**Self-Join**
A self-join is a join operation in SQL where a table is joined with itself. This is useful when you need to compare rows within the same table based on a related column. Essentially, you treat the single table as if it were two separate tables, allowing you to compare different rows of that table.

Example:
Given the following table.
![[Pasted image 20250419040053.png]]To find pairs of executives born in the same year, we need to compare the birthYear of one executive with the birthYear of every other executive in the same MovieExec table. A regular join would typically involve two different tables. Since we are comparing rows within the same table, we use a self-join.

```SQL
SELECT Exec1.name, Exec2.name
FROM MovieExec Exec1, MovieExec Exec2
WHERE Exec1.birthYear = Exec2.birthYear;
```
We essentially treat the MovieExec table as two separate instances and compare them with each other through the WHERE condition.

Note: More conditions should be added to the WHERE so you don't have duplicate values/the same tuple being selected

**SQL Join Expressions**
Different types of Joins:

<u>JOIN...ON...</u>:
We can join two relations R(A,B,C) and S(D,E,F) using the JOIN...ON... syntax.

Example:
```SQL
SELECT * 
FROM R JOIN S ON R.A = S.D AND R.B = S.E 
WHERE R.C = 100 AND S.F < 50;
```
Here, we specify which tables we join (R JOIN S) and then set the condition for joining them after the ON, which is R.A = S.D. The WHERE clause also has more conditions but observe that these are individual-relation conditions, that is you are not comparing R and S in the conditions in the WHERE clause. 

The SQL statement results in a view with the following schema: (R.A, R.B, R.C, S.D, S.E, S.F)

Also observe that JOIN...ON.. is somewhat optional, we can rewrite it and make it equivalent by just putting it in the WHERE conditions. However, using JOIN..ON.. makes it obvious on how tables are being joined
```SQL
SELECT * 
FROM R, S 
WHERE R.A = S.D AND R.B = S.E AND R.C = 100 AND S.F < 50;
```

<u>CROSS JOIN</u>:
A CROSS JOIN creates a cartesian product view of all the rows in two relations R and S, so that every row of R is paired with every row of S (i.e. if |R| = n and |S| = m, then a cross join yields n * m rows). The schema of the resulting relation is (R.A, R.B, R.C, S.C, S.D, S.E).

This is equivalent to 
```SQL
SELECT *
FROM R, S;
```
* Nobody uses CROSS JOIN syntax over just using commas.

![[Pasted image 20250504222547.png]]


<u>NATURAL JOIN</u>:
Join that combines the same attributes across tables.
`R NATURAL JOIN S`, yields the schema (A, B, C, D, E) (note that C only appears once when both relations have it as an attribute).

This is equivalent to:
```SQL
SELECT R.A, R.B, R.C, S.D, S.E 
FROM R, S 
WHERE R.C = S.C;
```

**Set and Bag Operations**
Assuming given example relations of R(A,B,C), and S(AB,C)

<u>Set Union</u>: 
A set union combines the results of two or more SELECT statements into a single **set**. The unioned relations must have attributes of the same type in the same order (union compatibility) for this to happen (but the attributes/names can be different) and the output naturally has the same schema as R and S. 

Also, since this is a **set** union and the result is a set, then duplicate rows are automatically removed to maintain the set property. 

For reference if R has n rows and S has m rows, and all are distinct, then we end up with m + n rows after the union operator.

Syntax: UNION
```SQL
( SELECT * FROM R WHERE A > 10 )
UNION 
( SELECT * FROM S WHERE B < 300 );
```

<u>Bag (Multiset) Union</u>:
A bag union is the same as a set union except instead of a set result, it is a bag result meaning that duplicate tuples are allowed. However, 

Syntax: UNION ALL
```SQL
( SELECT * FROM R WHERE A > 10) 
UNION ALL 
( SELECT * FROM S WHERE B < 300 );
```

<u>Set and Bag (Multiset) Intersection and Difference (Except)</u>
Like unions, for intersections and differences (also binary operators like unions) to occur you need the relations to be union-compatible. The outputs are also the same type as the input relations R or S.

Set Intersection: `QUERY1 INTERSECT QUERY2`
Bag Intersection: `QUERY1 INTERSECT ALL QUERY2`
- Finds all tuples that are in the results of both QUERY1 and QUERY2
- For bag intersection, since bags can have duplicate rows if there's duplicate rows in either relation, it takes the minimum of the row count in query1 or query2 to put in the resultant intersection bag. So if R had 2 same rows and S had 3 same rows, the bag intersection would have the 2 same rows since it's the minimum row count for both relations (basically min(m, n))

Set Difference: `QUERY1 EXCEPT QUERY2`
Bag Difference: `QUERY1 EXCEPT ALL QUERY2`
- Finds tuples that are in the result of QUERY1 but not in QUERY2.
- For bag difference if there's same rows in both queries, we do num rows in query1 - num rows in query2 and that many rows is in the final bag difference result. However, if there are more same rows in query2 than in query1, instead of going negative we just treat it as 0 and don't include any of those same rows in the final bag difference since there's more same rows in query2 instead of query1. so essentially max(m - n, 0)

**Order of Operations**
In general, order of operations is left-to-right, so `Q1 EXCEPT Q2 EXCEPT Q3 == ( Q1 EXCEPT Q2 ) EXCEPT Q3`, but INTERSECT has **higher priority** than UNION and EXCEPT

**Subqueries**
A subquery is a query that is embedded in another query. So like UNION would have two subqueries.

Subqueries can either return a constant (scalar value) used in a WHERE clause or boolean expression or a relation used in a FROM clause.

Example: Subquery returning constants
In the following query, we use a subquery to try to get the producerC# of a row in another table that matches with the MovieExec cert#. In this instance, the subquery returns a constant to be used in a WHERE clause.
```SQL
SELECT e.execName
FROM MovieExec e
WHERE e.cert# = (SELECT m.producerC# FROM Movies m WHERE m.movieTitle = 'Star Wars');
```
However!! There's a trap here - if the subquery returns one row, it's fine, and if it returns nothing, it's also fine because that means the subquery will just result in NULL. However, what happens if it returns two or more rows? Then how does the e.cert# compare itself to the rows in the main WHERE clause? 
Answer: It doesn't. If this scenario happens you get a runtime error.

So, the correct way to write this would be to not use a subquery at all, but just pull data from the two tables directly.
```SQL
SELECT e.execName 
FROM Movies m, MovieExec e 
WHERE m.movieTitle='Star Wars' AND m.producerC# = e.cert#;
```

Example #2: Subquery returning relations
In this example, observe how our subquery returns a list of producerC# values from the Movies relation where the condition is satisfied. Then, in the outer statement we iterate through the MovieExec rows and select the execNames where the row's cert# value is in the subquery result array of producerC# values.
```SQL
SELECT e.execName 
FROM MovieExec e WHERE e.cert# 
IN (SELECT m.producerC# FROM Movies m WHERE m.movieTitle = 'Star Wars');
```

Question: Is the above statement equal to:
```SQL
SELECT e.execName 
FROM MovieExec e, Movies m 
WHERE m.movieTitle = 'Star Wars' AND m.producerC# = e.cert#
```
Answer: Yes, both statements are functionally equivalent.

Example #3: Subquery returning a relation into the FROM clause
Question: Are the following queries the same?
```SQL
SELECT e.execName 
FROM MovieExec e, (SELECT m.producerC# FROM Movies m WHERE m.movieTitle = 'Star Wars') p 
WHERE e.cert# = p.producerC#
```

```SQL
SELECT e.execName
FROM MovieExec e, Movies m 
WHERE e.cert# = m.producerC# AND m.movieTitle = 'Star Wars';
```

In the first query, we use a subquery to retrieve a relation, give it an alias p and use it for our WHERE clause.

Note: The subquery returns a table/relation p with only one column/attribute, producerC#. That's why we still need to reference it via p.producerC# even though there's only one value

Indeed, the two statements are equal. They both have the same logic, just applied in different orders - Question -> what is a similar statement one could make as a trick question for statements that are not equal to the one above ^ ask LLMs

**Subqueries with Subqueries**
A somewhat tedious way to query for MovieExecs of Harrison Ford movies
```SQL
SELECT e.execName
FROM MovieExec e
WHERE e.cert# IN (SELECT m.producerC# 
	FROM Movies m
	WHERE (m.movieTitle, m.movieYear) IN 
		(SELECT s.movieTitle, s.movieYear
		FROM StarsIn s
		WHERE s.starName = 'Harrison Ford')
```

This can be re-written as:
```SQL
SELECT e.execName 
FROM MovieExec e, Movies m, StarsIn s 
WHERE e.cert# = m.producerC# AND m.movieTitle = s.movieTitle AND m.movieYear = s.movieYear AND s.starName = 'Harrison Ford';
```

Also can be re-written as (using JOIN):
```SQL
SELECT e.execName 
FROM MovieExec e 
	JOIN Movies m ON e.cert# = m.producerC# 
	JOIN StarsIn s ON (m.movieTitle, m.movieYear) = (s.movieTitle, s.movieYear) 
WHERE s.starName = 'Harrison Ford';
```

**Correlated Subqueries**
Correlated subqueries are subqueries where the inner query (subquery) depends on attributes in the outer query, and this process is called correlation.

Example: Find the movie titles that have been used for two or more movies.
```SQL
SELECT DISTINCT m.movieTitle
FROM Movies m
WHERE m.movieYear < 
ANY (SELECT m2.movieYear
	FROM Movies m2
	WHERE m2.movieTitle = m.movieTitle);
```
In this example, we refer to m inside our subquery to find if any movieTitles in the Movies relation are the same or not. We also then use the comparison of movieYear in the WHERE clause to ensure that they are different movies by checking the movieYear is less than at least one of the other movieYears so that we know the movieTitle has been used for 2+ movies.

Example #2: Finding stars that have been in more than one movie in a year
```SQL
SELECT s.starName 
FROM StarsIn s 
WHERE EXISTS ( SELECT * 
	FROM StarsIn c 
	WHERE s.movieTitle <> c.movieTitle 
		AND s.movieYear = c.movieYear 
		AND s.starName = c.starName );
```

**Set Comparison Operators**
- x IN Q
	- Returns true if x occurs in the collection Q
- x NOT IN Q
	- Returns true if x does not occur in the collection Q
- EXISTS Q
	- Returns true if Q is a non-empty collection
- NOT EXISTS Q
	- Returns true if Q is an empty collection
- x op ANY Q, x op ALL Q in WHERE clause
	- x is a scalar expression
	- Q is a SQL query
	- op is one of { <, <=, >, >=, <>, = }.
- x op ANY Q
	- evaluates to TRUE if x satisfies the comparison operator op when compared to at least one value in Q, false otherwise (unless its unknown cause Q is null)
- x op ALL Q
	- evaluates to TRUE if x satisfies the comparison operator op when compared to all values in Q, false otherwise (unless all values in q satisfy and the rest are null, then evaluates to unknown)

**SQL Aggregates**
SQL has 5 aggregation operators (AGGOP) which are used for computing summary results over a table, like finding the average score of all students in a class. These operators are applied on scalar values (like 1.1 * salary or salary) aside from the COUNT operator, and are specified in the SELECT clause. They also all return a scalar value.

The SQL aggregation operators are:
- COUNT(A)
	- Returns the number of non-NULL values in the A column
- SUM(A)
	- Returns the sum of all non-NULL values in the A column
- AVG(A)
	- Returns the average of all non-NULL values in the A column
- MAX(A) and MIN(A)
	- Return the maximum value or minimum non-NULL value in the A column
For the COUNT, SUM, and AVG operators, you can optionally specify a DISTINCT clause next to the column (like DISTINCT A) to apply the operator to only DISTINCT/different non-NULL values.

Example:
Given the following table:
![[Pasted image 20250504201549.png]]

Find the average net worth of all MovieExecs.

Answer: `SELECT AVG(netWorth) FROM MovieExec;`
- note: returns a scalar value instead of the traditional rows! so you can do stuff like
```SQL
SELECT column1, column2
FROM SomeOtherTable
WHERE SomeValue > (SELECT AVG(netWorth) FROM MovieExec);
```

Example 2:
Given the SQL query:
```SQL
SELECT MAX(length), MIN(length) FROM Movies;
```
since they return both scalar values, remember to attach alias to them so you can use them in other parts of queries like so:
```SQL
SELECT m.title, m.length, limits.max_len, limits.min_len
FROM Movies m, -- The comma implies a CROSS JOIN here since there's no ON clause
     (SELECT MAX(length) AS max_len, MIN(length) AS min_len FROM Movies) AS limits
WHERE m.length = limits.max_len OR m.length = limits.min_len;
-- This example finds the movies that actually have the max or min length --
```

**GROUP BY**
The `GROUP BY` clause is used for grouping things together, commonly attribute values. For instance, given a movies relation Movies(movieTitle, movieYear, length, genre, studioName, producerC#), observe how the following query finds the sum of lengths of all movies for each studio.
```SQL 
SELECT studioName, SUM(length)
FROM Movies
GROUP BY studioName;
```

The GROUP BY clause follows the WHERE clause and is structured like so:
![[Pasted image 20250504202530.png]]

Procedure: Notice how the GROUP BY operator is applied FIRST, AND THEN the aggregate operators.
![[Pasted image 20250504204919.png]]

Example:
The two statements are equivalent (but NEVER use GROUP BY unnecessarily to remove duplicates, just use DISTINCT):
```SQL
-- Statement 1 (no GROUP BY)
SELECT DISTINCT studioName FROM Movies;

-- Statement 2 (using GROUP BY)
SELECT studioName FROM Movies GROUP BY studioName;
```

Example 2:
```SQL
SELECT e.execName, AVG(m.length)
FROM MovieExec e, Movies m
WHERE m.producerC# = e.cert#
GROUP BY e.execName;
```
This statement shows each movie exec's name and the average length of movies made by that exec. Note how it can be potentially wrong because all execs with the same name are grouped together even if they have different cert# values. So if we had two John Smith's, because the GROUP BY operator works first, it creates a John Smith group. Then, the AVG AGGOP operates on the length values belonging to ALL John Smiths, giving a combined weird and wrong average value.

Example 3:
Given this reference table:
![[Pasted image 20250504205437.png]]

1. What is the result of this query?
```SQL
SELECT A, B, SUM(C), MAX(D)
FROM R
GROUP BY A, B;
```
This query creates groups of unique A, B pairs like a1, b1, then finds the sum of all the C attributes and the max of the D attributes that have their A, B value equal to the group. It will finally display the A, B values that define the group along with the calculated SUM(C) and MAX(D). For instance, for a1, b1, SUM(C) = 1 + 2 + 6 since those rows all have a1, b1, while max(D) is 12.

2. What if the query asked for B after SUM(C)?
	- If the query asked for B after SUM(C), only the order of columns changes since SELECT ordering affects presentation order only, not actual data.
3. What if the query didn’t ask for A in the SELECT?
	- The query is still valid since it's only invalid if there's a SELECT attribute that's not an AGGOP or in the GROUP BY clause as well. The only difference is that since the A column won't be in the resulting view table, there may be some duplicate values like 2 b1's, but these are referring to different groups (one for a1, one for a2) so it may be hard to differentiate. But the presentation and data is still the same and the query is valid.
4. Does SELECT DISTINCT have any effect when there’s a GROUP BY?
	- No, because the GROUP BY clause inherently produces only one unique row for each distinct combination of the grouping keys.
- What if we SELECT DISTINCT on just B, SUM(C), MAX(D) (so don't include A in the select attributes query)?
	- Then in that case, if we have two identical rows of B, SUM(C), MAX(D), but there's two since they correspond to different A's/A, B pairings, then DISTINCT would take effect and eliminate one of them in the final result view

**Aggregation and GROUP BY Properties**

<u>Conflict</u>:
Note that if you have a GROUP BY clause, the attributes that you put in the SELECT clause MUST be also in the GROUP BY clause or else you'll get an error, since GROUP BY works on the entire table summary/groups. Also, if you have an aggregation operator in the SELECT clause, ALL the other attributes in the SELECT clause must either also be in the GROUP BY clause, or also be aggregation operators. You cannot select table summary-type elements like the group by/aggregation op with more single-row oriented elements like a name attribute.

Essentially, aggregate-types process multiple inputs to make a single output, while row-types output one row for each input row that matches, so due to this conflict in level of detail the two are incompatible in a final query and cannot be selected together.

It is also possible to write GROUP BY without aggregates and vice versa.

<u>NULL Interactions</u>:
NULLs are ignored in any aggregation, so they do not contribute to the SUM, AVG, COUNT, MIN, MAX of an attribute. 
- However for COUNT there are two edge cases:
	- COUNT(\*) = number of tuples in a relation (even if some columns are null)
	- COUNT(A) is the number of tuples with non-null values for attribute A
- SUM, AVG, MIN, MAX on an empty result (no tuples) is NULL
	- COUNT of an empty result is 0
- GROUP BY does NOT ignore NULLs
	- GROUP BY treats NULL as a value, meaning it can be its own group and group all the rows with NULL in that attribute together (the NULL group)

Example: 
Given the relation R(A, B) which contains a single tuple (NULL, NULL)
1. What does `SELECT A, COUNT(B) FROM R GROUP BY A;` do?
	1. returns a row of NULL, 0 cause count(B) is num of tuples with non-null values while NULL is the group name
2. What does `SELECT A, COUNT(*) FROM R GROUP BY A;` do?
	- returns a row of NULL, 1 cause count(\*) gives num of tuples in a relation including null ones
3. What does `SELECT A, SUM(B) FROM R GROUP BY A;` do?
	- returns a NULL, NULL row because sum returns null on empty result

**HAVING Clause**
The HAVING clause let's you choose your groups in a GROUP BY statement based on some aggregate property of the group itself (like a where clause but applied to groups). Since it is reliant on groups, you cannot use it without the GROUP BY clause. Note that the same attributes and aggregates that can appear in the SELECT clause can also appear in the HAVING clause (so match aggregate types and row-oriented types together don't combine them. this is the same property that SELECT and GROUP BY share)

Structure: 
![[Pasted image 20250504220514.png]]

Updated Procedure:
![[Pasted image 20250504220527.png]]

<u>In the below examples, we assume that MovieExec.execName is UNIQUE</u>

Example:
Find the name and total film length for just those producers who made
at least one film prior to 1930.
```SQL
SELECT e.execName, SUM(m.length)
FROM MovieExec e, Movies m
WHERE m.producerC# = e.cert#
GROUP BY e.execName
HAVING MIN(m.movieYear) < 1930;
```

Example 2:
Find the total film length for just those producers who made films in at least 4 different years.
```SQL
SELECT e.execName, SUM(m.length)
FROM MovieExec e, Movies m
WHERE m.producerC# = e.cert#
GROUP BY e.execName
HAVING COUNT(DISTINCT m.movieYear) >= 4;
```

Example 3:
Find the total film length and the latest movie year, for just those producers who made movies in at least 4 different years, and who made at least one film prior to 1930.
```SQL
SELECT e.execName, SUM(m.length), MAX(m.movieYear)
FROM MovieExec e, Movies m
WHERE m.producerC# = e.cert#
GROUP BY e.execName
HAVING COUNT(DISTINCT m.movieYear) >= 4
 AND MIN(m.movieYear) < 1930;
```
Note: We are assuming that execName is UNIQUE, but if it isn't then we risk running into scenarios where there are two execs with the same name, like 'John Smith', it counts the distinct years in which any of the John Smiths produced a movie. Then, a group might falsely satisfy the HAVING condition because, collectively, the different executives with that name made films across 4 or more distinct years, even if no single executive named 'John Smith' did so individually. There are also other parts that are impacted like the same thing happening to satisfy MIN, and the SELECT values of SUM and MAX being wrong too.

Example 4:
Find the minimum age of sailors in each rating category such that the minimum age of the sailors in that category is greater than the average age of all sailors.
```SQL
SELECT S.rating, MIN(S.age)
FROM Sailors S
GROUP BY S.rating
HAVING MIN(S.age) > ( SELECT AVG(S2.age)
 FROM Sailors S2 );
```


Example 5:
Find the second minimum age of sailors.
```SQL
SELECT MIN(S.age)
FROM Sailors S
WHERE S.age > ( SELECT MIN(S2.age)
 FROM Sailors S2 );
```

1. What happens when there is only one sailor? 
	- Because there is only one row, the WHERE clause is never satisfied due to there not being any age greater than the only value of age. Thus, the outer MIN function is therefore applied to an empty set of rows and returns NULL
2. What happens when all sailors have the same age? 
	- This leads to the same scenario has the previous one where since the WHERE clause is never satisfied, it returns an empty set of rows which MIN evaluates and returns NULL
3. What happens when there are no sailors? 
	- The inner subquery runs on an empty table and returns NULL. Since we have a NULL comparison in the WHERE clause, this returns UNKNOWN and since it's UNKNOWN it's not return in the result view. Also, the outer query operates on an empty table too due to the FROM statement and thus the MIN runs on an empty set and returns NULL
4. Can you figure how to find the third minimum age of sailors?
	- Yes, we can extend the logic. The original query found the second minimum by finding the minimum age greater than the first minimum. To find the third minimum, we need to find the minimum age that is greater than the second minimum. We can achieve this by nesting the subqueries further.
```SQL
-- Find the third minimum age of sailors
SELECT MIN(S.age) -- Select the minimum age...
FROM Sailors S
WHERE S.age > ( -- ...that is greater than the second minimum age.

                  -- This inner part calculates the second minimum age
                  SELECT MIN(S2.age)
                  FROM Sailors S2
                  WHERE S2.age > ( -- ...by finding the minimum age greater than the first minimum age.

                                   -- This innermost part calculates the first minimum age
                                   SELECT MIN(S3.age)
                                   FROM Sailors S3
                                 )

                );
```

Example 6: Illegal Examples
In the following examples, each statement is illegal/not valid SQL because aggregates can't appear in WHERE clauses, except in legal subqueries like the previous examples.
```SQL
-- Statement 1
SELECT MIN(S.age)
FROM Sailors S
WHERE S.age > MIN(S.age);

-- Statement 2
SELECT MIN(S.age)
FROM Sailors S
WHERE S.age > MIN(S2.age);

-- Statement 3
SELECT MIN(S.age)
FROM Sailors S
WHERE S.age > ( MIN(S2.age)
 FROM Sailors S2 );

-- Statement 4
SELECT MIN(S.age)
FROM Sailors S
WHERE S.age > MIN ( SELECT S2.age
 FROM Sailors S2 );
```

**Count Examples**
Given the following tables:
![[Pasted image 20250506183417.png]]

Example 1:
Find the total number of customers
```SQL
SELECT COUNT(cid)
FROM Customers;
```

Example 2:
Find the total number of days that customers engaged in activities
```SQL
SELECT SUM(DISTINCT day)
FROM Activities
```

Example 3:
Find the number of activities done by advanced customers
```SQL
SELECT COUNT(a.cid)
FROM Customers c, Activities a
WHERE c.cid = a.cid
	AND c.level = 'Advanced';
```

Example 4:
Find the number of activities done by different advanced customers.
```SQL
SELECT COUNT(DISTINCT a.cid)
FROM Customers c, Activities a
WHERE c.cid = a.cid
	AND c.level = 'Advanced';
```
1. Would these queries have same results with COUNT(c.cid) instead of COUNT(a.cid)?
	- yes
2. What about if queries had COUNT(\*)/COUNT(DISTINCT \*) instead?
	- no

Example 5: 
For each day, find the number of activities that were done by advanced customers.
```SQL
SELECT a.day, COUNT(a.cid)
FROM Customers c, Activities a
WHERE c.cid = a.cid
	AND c.level = 'Advanced';
GROUP BY a.day
```

Example 6:
For each day, find the number of different advanced customers who did at least one activity.
```SQL
SELECT a.day, COUNT(DISTINCT a.cid)
FROM Customers c, Activities a
WHERE a.cid = c.cid
 AND c.level = ‘Advanced’
GROUP BY a.day;
```

Example 7:
For each level and day, find the number of activities done that day by customers at that level.
```SQL
SELECT c.level, a.day, COUNT(a.cid)
FROM Customers c, Activities a
WHERE a.cid = c.cid
GROUP BY c.level, a.day;
```

Example 8:
For each level and day, find the number of activities done that day by different customers at that level.
```SQL
SELECT c.level, a.day, COUNT(DISTINCT a.cid)
FROM Customers c, Activities a
WHERE a.cid = c.cid
GROUP BY c.level, a.day;
```

Example 9: 
For each customer level, find the number of times that customers who are at that level went on a red slope, giving level as well as number of times, … but only if the number of times is at least 3. – The number of times should appear as redCount in the result
```SQL
SELECT c.level, COUNT(*) as redCount
FROM Customers c, Activities a, Slopes s
WHERE c.cid = a.cid
	AND a.slopeid = s.slopeid
	AND s.color = 'Red'
GROUP BY c.level
HAVING COUNT(*) >= 3;	
```

**SUM & AVG Examples**
Example 1:
Find the total revenue of the company, assuming Sales has qty and price columns.
```SQL
SELECT SUM(qty*price) 
FROM Sales;
```

Example 2:
Find the average salary of employees in the Marketing department.
```SQL
SELECT AVG(salary)
FROM Employees
WHERE department='Marketing';
```

**MIN & MAX Examples**
Example 1:
Find the name and age of the oldest snowboarders.

Wrong because can't mix aggregate and non-aggregate columns in select
```SQL
SELECT c.cname, MAX(c.age)
FROM Customers c
WHERE c.type=‘snowboard’;
```

Wrong because you're only filtering for snowboarder type in subquery not in outside query, people of other types can still appear when you only want snowboarders.
```SQL
SELECT c.cname, c.age
FROM Customers c
WHERE c.age = (SELECT MAX(c2.age)
 FROM Customers c2
 WHERE c2.type=‘snowboard’);
```

Example 2:
Find the age of the youngest participant for each type of activity that Beginners participate in.

Wrong cause mixing non-aggregate and aggregate types. Also note that if you just have aggregate types in the SELECT clause, it selects from the whole group filtered out by the WHERE
```SQL
SELECT c.type, MIN(c.age) 
FROM Customers c 
WHERE c.level=‘Beginner’;
```

Example 3:
Find the age of the youngest participant for each type of activity that Beginners participate in.

Wrong because it selects age of youngest <u>Beginner</u> for activity types that Beginners
participate in
```SQL
SELECT c.type, MIN(c.age)
FROM Customers c
WHERE c.level=‘Beginner’
GROUP BY c.type;
```

Right version:
```SQL
SELECT c.type, MIN(c.age)
FROM Customers c
WHERE EXISTS ( SELECT *
 FROM Customers c2
 WHERE c2.level=‘Beginner’
 AND c2.type=c.type )
GROUP BY c.type;
```

Also right:
```SQL
SELECT c.type, MIN(c.age)
FROM Customers c
GROUP BY c.type
HAVING SOME ( c.level=‘Beginner’ );
```

**EVERY and SOME Clauses**
The SOME clause is a synonym for the ANY clause and can be used interchangeably.
The EVERY keyword is **NOT THE SAME** as the ALL keyword and behaves different. It is used as a boolean aggregate function, while ALL is used with comparison operators to compare a scalar value against a set of values returned by a subquery.

For instance, ALL can be used like `WHERE age > ALL (SELECT age FROM children)`
While EVERY is used like so:
```SQL
SELECT S.rating, MIN (S.age)
FROM Sailors S
WHERE S.age >= 18
GROUP BY S.rating
HAVING COUNT (*) > 1 AND EVERY (S.age <= 40);
```

ANY/SOME in HAVING is not supported by PostgreSQL but valid for exams since it's in SQL standard, meanwhile EVERY is valid for both.


**Edge Cases/Carefulness**

Example 1:
Find the activities of Luke.
```SQL
SELECT *
FROM Activities a
WHERE a.cid = (SELECT c.cid
 FROM Customers c
 WHERE c.cname=‘Luke’);
```
If there's only one Luke in the Customers table the subquery works and returns one cid value. But if there's more than one Luke you get more than one value returned from the subquery and a run-time error occurs.

Example 2:
Find the names of all customers whose age is greater than the age of every/some snowboarder. (first two attempts try to find greater than every age, last two attempts are for greater than some ages).

Let's evaluate what were to happen if there's no snowboarders

Four attempts: We're just changing the aggregate operator in all of them (ALL, MAX, SOME, MIN)
```SQL
SELECT c.name
FROM Customers c
WHERE c.age > ALL (SELECT c2.age
 FROM Customers c2
 WHERE c2.type = ‘snowboard’);
```
- if no snowboarders, returns empty set and where condition evaluates to TRUE for all customers. This is because, logically, the statement "this customer's age is greater than every age in an empty list of ages" is vacuously true.
```SQL
SELECT c.name FROM Customers c 
WHERE c.age > (SELECT MAX(c2.age) 
		FROM Customers c2 
		WHERE c2.type = ‘snowboard’);
```
- since it's an empty set, MAX would return NULL, and the where condition since it's evaluating vs. a NULL would return UNKNOWN. rows with an UNKNOWN condition in the WHERE are filtered out (treated as false) so query would return no customers. this is preferred.
```SQL
SELECT c.name
FROM Customers c
WHERE c.age > SOME (SELECT c2.age
 FROM Customers c2
 WHERE c2.type = ‘snowboard’);
```
- since it's an empty set, this would actually return false. For c.age > SOME (set) to be true, there must exist at least one value s in the set such that c.age > s. If the set is empty, no such value exists, so the condition is false. so query returns no customers and is also preferred
```SQL
SELECT c.name
FROM Customers c
WHERE c.age > (SELECT MIN(c2.age)
 FROM Customers c2
 WHERE c2.type = ‘snowboard’)
```
 - since it's an empty set, MIN would return NULL, and the where condition since it's evaluating vs. a NULL would return UNKNOWN. rows with an UNKNOWN condition in the WHERE are filtered out (treated as false) so query would return no customers. this is preferred

<u>SOME/ANY vs. ALL</u>
SOME/ANY are existential quantifiers, meaning that they're only true if there is **at least one element** in the set for which the condition is true. That's why if you encounter an empty set they become FALSE because there's no existence of elements in the set to make the definition true.

Meanwhile for ALL, this is a universal quantification and is only true if every single element in the set satisfies a condition. So if there's no element in a set in which the condition is false (no counter examples), then it evaluates to true, such as the case with an empty set.

**Relaxing Restrictions**
Given the instructions "For each cid and day for which that customer did an activity, give cid, level and day, with COUNT of number of activities done on that day", this statement is correct:
```SQL
SELECT c.cid, c.level, a.day, COUNT(*)
FROM Customers c, Activities a
WHERE a.cid = c.cid
GROUP BY c.cid, a.day;
```
Even though level isn't a GROUP BY attribute, in most SQL databases this still works since cid is the PK of Customers and is thus not ambiguous. This is allowed on HWs and exams but only if done correctly.

Other solutions:
```SQL
SELECT c.cid, c.level, a.day, COUNT(*)
FROM Customers c, Activities a
WHERE a.cid = c.cid
GROUP BY c.cid, c.level, a.day; -- c.level is now included
```

```SQL
SELECT
    c.cid,
    c.level,
    AggregatedActivities.day,
    AggregatedActivities.activity_count
FROM
    Customers c
JOIN
    (SELECT
         a.cid,
         a.day,
         COUNT(*) AS activity_count
     FROM
         Activities a
     GROUP BY
         a.cid, a.day) AS AggregatedActivities
ON
    c.cid = AggregatedActivities.cid;
```


**Database Modification Statements**
SQL's modification operations are INSERT, DELETE, and UPDATE. They are all DML (Data Manipulation Language) statements like SELECT.

These modification operations change the state of the database and do not return any rows/values, but might return errors/error codes.

Database modification statements are completely evaluated on the old state of the
database, producing a new state of the database.

**INSERT**
A tuple $(v_1 , …, v_n)$ is inserted into the relation R, where attribute $A_i = v_i$ and default values (perhaps NULL) are entered for all missing attributes.

Example:
```SQL
INSERT INTO StarsIn(movieTitle, movieYear, starName)
VALUES ('The Maltese Falcon', 1942, 'Sydney Greenstreet');
```
- This approach is preferred and more robust
- The order of the values in the VALUES clause must match the order specified in your (movieTitle, movieYear, starName) list, not the physical order of columns in the table. 
- This allow allows for inserting into just a few columns when the table might have a ton of columns
Can also do:
```SQL
INSERT INTO StarsIn
VALUES ('The Maltese Falcon', 1942, 'Sydney Greenstreet');
```
Valid only if:
- The VALUES clause provides a value for every column in the StarsIn table.
- The values are provided in the exact same order as the columns are defined in the table's schema (i.e., the order established when the table was created).


<u>Insertion Edge Cases: </u>
Order: Inserted value -> default row value -> NULL -> error
1. If a new row is inserted into MovieStar, and a value is specified for an attribute, then that attribute will receive that value.
2. If a new row is inserted into MovieStar, and no value is specified for an attribute, and that attribute has a DEFAULT (e.g., for address), then the value for that attribute will be that DEFAULT, which is 'Hollywood' for address.
3. If a new row is inserted into MovieStar, and no value is entered for an attribute, and the value of that attribute does not have a DEFAULT, and the value of that attribute is allowed to be NULL (e.g.,for birthdate), then the value for the attribute will be NULL.
4. If a new row is inserted into MovieStar, and no value is entered for an attribute, and the value of the attribute does not have a DEFAULT, and the value is not allowed to be NULL (e.g., for gender), then an error will be raised.

Example 2:
Add to the relation Studio all the studio names that appear in the studioName column of Movies but do not already occur in the studio names in the Studio relation.
```SQL
INSERT INTO Studio(studioName)
	SELECT DISTINCT m.studioName
	FROM Movies m
	WHERE m.studioName NOT IN
		(SELECT st.studioName
		FROM Studio st);
```
- note that we use DISTINCT to avoid redundancy in the insertion!!

Example 3:
Puts the Disney2018Movies into the Movies table, setting movieYear and studioName values using constants.
```SQL
INSERT INTO Movies(movieTitle, movieYear, length, studioName)
	SELECT dm.movieTitle, 2018, dm.length, 'Disney'
	FROM Disney2018Movies dm;
```

**DELETE Clause**
Format:
```SQL
DELETE FROM R
	WHERE <condition>;
```

Example:
```SQL
DELETE FROM StarsIn
WHERE movieTitle = 'The Maltese Falcon'
 AND movieYear = 1942
 AND starName = 'Sydney Greenstreet'
```
- The tuple ('The Maltese Falcon', 1942, 'Sydney Greenstreet') will be deleted from the relation StarsIn.

Example 2:
Deletes all movie executives whose net worth is less than 10 million dollars
```SQL
DELETE FROM MovieExec
	WHERE netWorth < 10000000;
```

Example 3:
Deletes all movie executives who produced movies starring Sydney Greenstreet
```SQL
DELETE FROM MovieExec
WHERE cert# IN
(SELECT m.producerC#
 FROM Movies m, StarsIn s
WHERE m.movieTitle = s.movieTitle AND m.movieYear = s.movieYear
 AND s.starName = 'Sydney Greenstreet');
```

Example 4:
What does `DELETE FROM MovieExec;` do?
Answer: Deletes all the tuples from MovieExec!!!

**UPDATE Clause**
Format:
```SQL
UPDATE R
	SET <new-value-assignments>
	WHERE <condition>;

-- expand new value assignment into
<new-value-assignment> :-
	<attribute> = <expression>, …, <attribute> = <expression>
```

Examples:
```SQL
UPDATE Employees
SET salary = 85000, dept = 'SALES'
WHERE Ssnum = '123456789';

UPDATE Employees
SET salary = 25000
WHERE salary IS NULL;

UPDATE Employees
SET salary = salary * 1.1
WHERE salary > 100000;
```

Example 2: With subquery
```SQL
UPDATE MovieExec
	SET execName = 'Pres. ' || execName
	WHERE cert# IN ( SELECT presC# FROM Studio );
```
- The || operator can be used to concatenate the string 'Pres. ' with execName

Example 3: 
In this example we use the FROM clause to get data from another table. This update sets all seats to not paid in the tickets relation.
```SQL
UPDATE Tickets t
SET seatNum = newt.seatNum, paid = FALSE
FROM NewTickets newt
WHERE t.ticketID = newt.ticketID
AND t.custID = newt.custID
 AND t.airlineID = newt.airlineID
 AND t.flightNum = newt.flightNum
```
- The attributes in the SET MUST appear WITHOUT the tuple variable, so you can't do like `SET t.seatNum`, but for UPDATE, FROM, WHERE you can use the tuple variable/alias

Example 4:
```SQL
UPDATE MovieExec e
 SET netWorth = 6000000
 WHERE NOT EXISTS (SELECT * FROM MovieExec e2
 WHERE e2.Net
```

**SQL Transactions**
<u>One-Statement-At-a-Time Semantics</u>
Before we were just updating the DB one query/modification at a time
![[Pasted image 20250507131317.png]]

But in real life if there's concurrent operations or a crash in between statements, you get a lot of issues.

This is where transactions come in. A transaction is a collection of one or more operations on the database that execute **atomically**, meaning that either all operations execute or none of them do.

Transactions follow ACID properties:
- Atomicity: All statements in a transaction succeed or none do.
- Consistency: Database remains in a valid state before and after the transaction.
- Isolation: Concurrent transactions do not interfere with each other.
- Durability: Once a transaction is committed, its changes are permanent.

<u>SQL Transactions</u>
BEGIN TRANSACTION
- Marks the beginning of a transaction, followed by one or more SQL statements.

COMMIT
- Ends the transaction. All changes to the database caused by the SQL statements within the transaction are committed (i.e., they are permanently there -- Durability) and visible in the database.

ROLLBACK
- Causes the transaction to abort or terminate. Any changes made so far by SQL statements within the transaction are undone (“rolled back”)

Once Committed, the Transaction is DURABLE.

Example:
```SQL
BEGIN TRANSACTION;
UPDATE Accounts SET balance = balance + 100 WHERE account_num = 456
UPDATE Accounts SET balance = balance - 100 WHERE account_num = 123 
COMMIT;
```

Example 2:
Consider the following transaction T that transfers an amount of money ($X)
from one account to another:
1. Add $X to Account 2.
2. Test if Account 1 exists and has at least $X in it.
	- If there is insufficient money, subtract $X from Account 2.
	- Otherwise, subtract $X from Account 1.

```SQL
BEGIN TRANSACTION
	<SQL statement to add $100 to account 2>
	<SQL statement to check whether bank account 1 has >= $X>
		If account 1 has < $X, then ROLLBACK;
	<SQL statement to withdraw $100 from account 1>
COMMIT;
```

If we have two transactions that operate concurrently they may interfere with each other.
![[Pasted image 20250507132721.png]]
In this case of the two transactions operate concurrently you get an error, because first T1 deposits 150 in A2's account giving a balance of $350. When T2 checks if A2 has enough money for the transaction, it sees it does due to T1 depositing that amount and subtracts $250 due to the transfer leaving A2 with $100 and thus finishes. But now for T1, it sees that A1 does not have $150 and thus rolls back its deposit of A2 and subtracts 150 from A2's account leaving A2 with a final balance of -50 when both transactions should have failed and rolled back.

So now we violate consistency cause DB is not in valid state and isolation because transactions did not operate independently and interfered with each other.

**Serializability**
