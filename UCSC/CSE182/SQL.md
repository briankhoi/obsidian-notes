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
	- used to ask questions abt the database and modify it

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
	if row.year == 1990 and row.studio == "Disney":
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



