what is a relation vs schema
a relation is a set of tuples?

Relational algebra (RA) is a query language for manipulating data in a relational data model. It is usually not used directly as a query language.

In relational algebra, the operands are relations and the operators are data transformations (input and output are both relations; operators are either unary or binary ops).

Internally, RDBS transform SQL queries into tree/graphs that are similar to relational algebra expressions. Then, query analysis, transformations, and optimizations are performed based on these relational algebra expression-like representations.

Relational databases use multi-set/bags, but relational algebra is based on sets. There are multi-set variations of relation algebra, but this course will only focus on set-based relational algebra, which thus does not contain all the functionality of SQL like GROUP BY or ORDER BY

**Relational Data Operators**
The five basic operators ($\sigma$, $\pi$, x , U , - ) are independent of each other, meaning that you can't define any of the operators using the others. However, there are other important operators that can be expressed using them
- Theta Join, Join, Natural Join, Semi-Join
- Set Intersection
- Division ( / or ÷ ) (Division will not be discussed in CSE 182)
- Outer Join 

![[Pasted image 20250529092855.png]]

<u>Set Union, Intersection, Difference</u>:
- relations that are unioned must have schemas with identical sets of attributes and types for each attribute
- columns must also be ordered the same so that it matches up with each relation

<u>Selection Operator</u>:  $\sigma_{condition}(R)$
The selection operator is an unary operator where the input and output are relations of same schema. It takes relation R and extracts only the rows from R that satisfy the condition, where the condition is a logical combination using AND, OR, NOT like the conditions in SQL's WHERE clause.

 It also exhibits the following properties: 
 - $\sigma_{C1}(\sigma_{C2}(R)) = \sigma_{C1 \ AND \ C2}(R)$
- $\sigma_{C1}(\sigma_{C2}(R)) = \sigma_{C2}(\sigma_{C1}(R))$

Example:
![[Pasted image 20250529172620.png]]

<u>Projection Operator</u>: $\pi_{<attribute \ list>}(R)$
The projection operator is a unary operator that takes a relation R as an input, and for every tuple in R it outputs only the attributes appearing in attribute list (so the output is a relation with attributes in attribute list that must be also attributes of R). Duplicates are also eliminated due to set semantics.

Example:
![[Pasted image 20250529173112.png]]

Example 2:
![[Pasted image 20250529173125.png]]

<u>(Cartesian) Product:</u> $R \times S$
The cartesian product is a binary operator that takes two relations as input, R and S, and returns a relation of arity m + n. It essentially takes each tuple in R, and appends each tuple of S to it to form a new tuple so that in the end, there are m + n columns and m x n.

Example:
![[Pasted image 20250529173523.png]]


Example 2:
If we are given relations R and S defined by R(A, B, C) and S(A, E), if we compute the product of R and S, since they contain the common attribute A, what happens?

When we compute the cartesian product of two relations that have the same attribute names, the attribute names are prefixed with their relation name like R.A so they can be distinguished. Everything else is normal. Note how we see the attribute renaming in the result below:
![[Pasted image 20250601172206.png]]


<u>Theta-Join:</u> $R \bowtie_{\theta} S$
A theta join is a binary operator that takes two relations R and S, and outputs tuples from $R \times S$ that satisfy the condition $\theta$ (output structure is a relation consisting of all attributes from R and S).

Thus, we have $R \bowtie_{\theta}$

Also, if $\theta$ is an equality condition, then our theta join is called an "equi-join".

<u>Natural Join</u>: $R \bowtie S$
A natural join is a binary operator that takes two relations R and S and outputs all the tuples of R x S where all the attributes with the same name across R and S have the same value.
![[Pasted image 20250601172931.png]]

Formally, this is defined as:
![[Pasted image 20250601173012.png]]

Example:
What is the natural join when R and S have no common attributes?

Then, the natural join $R \bowtie S$ is equivalent to the cartesian product R x S.

Example 2: 
What's the natural join when R and S have only common attributes?

Then, the natural join results in $R \cap S$

**Algebraic Laws**
Algebraic laws are laws in relational algebra that are important in query processing and optimization, allowing us to transform the execution of a query into an equivalent one that may be less costly.
![[Pasted image 20250601172446.png]]

Laws:
![[Pasted image 20250601172538.png]]
![[Pasted image 20250601172546.png]]

Example of Communtativity:
![[Pasted image 20250601172657.png]]

**Housekeeping Operations**
There are two housekeeping operations that make RA queries easier to write. 

<u>Assignment (:=)</u>
Assignments are used to store RA expressions in temporary variables, making things easier to read, write, and understand.

Example:
Find all sailors who have a rating of 3 or whose age is at least 18.
![[Pasted image 20250601173350.png]]

<u>Renaming:</u> $\rho$ 
Renaming follows the structure:
$\rho_{S(A_1, ..., A_n)}(R)$ 

It takes an input of Relation R with attributes ${B_1, ..., B_n}$ and outputs the same relation with a renamed name S and attributes ${A_1,...,A_n}$.

Example:
We take the relation S with its attributes, and rename it to T with S.C = T.X and S.D = T.D, so that when we do a cartesian product, the attribute names of R.C and S.C don't conflict.
![[Pasted image 20250601173740.png]]

**RA Queries**
Example:
Find the student ID and name where the student is a CS major and has a GPA above 3.0

Answer and visualization:
![[Pasted image 20250601173909.png]]
![[Pasted image 20250601173936.png]]

Example 2:
Query: Find the student ID, grade, and instructor where the student scored more than 80 points in a course.
![[Pasted image 20250601174004.png]]

Example 3:
Find the student name and course name where the student had a grade that was more than 80 points in a course.
![[Pasted image 20250601174054.png]]

A potential way this could have been executed:
![[Pasted image 20250601174125.png]]

Another way this could have been executed:
![[Pasted image 20250601174207.png]]

Another way this could have been executed:
![[Pasted image 20250601174233.png]]

**Query Optimization**
A DBMS optimizes the runtime of queries by comparing execution plans and finding a good one (not necessarily the best).

How a query optimizer comes up with an execution plan.
![[Pasted image 20250601174259.png]]

It also keeps some statistics to help calculate the query cost:
![[Pasted image 20250601174442.png]]

There are also some other factors in here like how frequently stored statistics are updated and CPU, I/O, network costs.

**EXPLAIN Statement**
In SQL, you have an EXPLAIN statement that let you see how the DBMS plans to execute the query (query plan).

