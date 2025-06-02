**What is a Relational Data Model?**
A relational data model is one where data is described and represented by the mathematical concept of a relation.

It consists of 3 parts:
- relation: structure of the data
- relational algebra: operations on the data
- RI, constraints on tuples, etc.: constraints on the data

**Relations**
A relation is a structure with rows (tuples) and columns (attributes), where they are sets and more specifically, a subset of a Cartesian product of sets

**RBDMS components**
Upper half:
- Parsing SQL
- Query Optimization
- Plan Generation
- Security
Lower half:
- Storage
- Planning and execution
- Concurrency control and scheduling
- Logging and recovery

**Tuples and Relations**
Tuple:
A k-tuple is an ordered sequence of k values (not necessarily different)
- For example, (112, 'Ann', 'CS', 'F', 3.95) is a 5-tuple
- Imagine it as a row in your DB table
Relations:
- A k-ary relation is a subset of $D_1\times D_2\times ... \times D_k$ where each $D_i$ is a set of elements 
- A relation is a set of tuples, so it cannot have duplicates/duplicates rows
- D is the domain (or datatype) or the ith column of the relation
- Domains may be enumerated {'AMES', 'CMPS', 'TIM'} or standard types (INTEGER, FLOAT, DATE, ...)

In mathematical theory/databases, the order of columns *matters*, and the order of rows *doesn't matter*. However, in SQL the order of rows does matter.
Note: SQL and the relational data model don't agree with each other 100%

**Three Types of Relations in an RDBMS**
1. Stored relations (tables aka relation instances)
2. Views
	1. views are results of queries on tables
	2. views are not stored in the DB
3. Temporary results from computations, including answers to queries
The relational model is closed under composition and:
- set oriented
- functional (no side effects)
- non-procedural (declarative - meaning user says what they want to do, not how to do it)
- enables data-independence

**Attributes and Relation Schema**
- An attribute is the name of a column in a relation 
- A relation schema R is a sequence of attributes $A_1, ..., A_k$ written as $R(A_1, ..., A_k)$ where $A_i$ is the name of the $i_{th}$ column of the relation
	- Datatype is an elementary type for each attribute like integer, NOT list or structure or compound structure
	- Example: Student(studentID, name, major, gender, avgGPA)
- An instance of a relation schema is a relation that confirms to the relation schema (a random row) - needs to have the **same** types that the schema defines
- In a database, rows are tuples, # of columns is # of relations? like k value for k-ary relation

**First Normal Form (1NF)**
- Domains are atomic
- Major has to be a single value, not a list of values
- Address has to be a single value, not a structure
- Aka every cell needs to be one value and not a compound structure

**Superkeys and Keys**
A superkey is a subset of the attributes that completely determines the contents of the row. So, if we figure out the superkey's value, we can determine the other values in the row as there's only possibility for them.

Formally, we define it as, a superkey S for a relation schema R is a subset of the attributes of R such that there cannot be two different tuples in an instance of R that have the same value for <u>all</u> the attributes in S. Assuming that relations cannot have duplicates, then for any relation R there is always a superkey of R, namely all the attributes of R (attrib(R))

As for a key, a key is a superkey that is minimal. Formally, a key K for a relation schema R is a subset of the attributes of R such that:
- there cannot be two different tuples in an instance of R that have the same value for <u>all</u> the attributes in K (superkey definition)
- it is minimal, meaning that no proper subset of K has the above property (there's no subset of the key that's also a superkey)

All keys are superkeys, but some superkeys are not keys. There can also be multiple keys in general, so one key is chosen and defined as the **primary key** while the rest are **candidate keys**.

A superkey is a constraint on the allowable instances of relation R. Since every key is a superkey, a key is also constraint on the allowable instances of relation R.

Also, we underline certain attributes to denote that they are keys.

<u>Examples:</u>
1.
Say you have the tuple (1, 2, 3, 4, 5) where the first three columns are a superkey. That means that you cannot have another tuple in your table where the first three columns are also 1, 2, 3. Also, if (1, 2) was also a superkey and sufficient to determine all the other rows, then that means both (1, 2) and (1, 2, 3) are both superkeys and (1, 2, 3) wouldn't be a key since it's not minimal due to one of its subsets being a superkey, however (1, 2) is.

2.
Given the schema:
Student(studentID, name, major, gender, avgGPA)
- {studentID} would be a key, because two different students can’t have the same studentId. It is also a superkey.
- {studentID, name} is a superkey, but it’s not a key.
- {studentID, name, major, gender, avgGPA} is a superkey but it’s not a key.

3.
Given the schema:
Student(studentID, name, dob, major, gender, avgGPA)
- {studentID}, {name, dob} are keys and also superkeys.
- {studentID} is the Primary Key. 
- {name, dob} is the candidate key.  
- {name, dob, avgGPA} is a superkey.

4.
Given the table:

| A   | B   | C   | D   |
| --- | --- | --- | --- |
| 1   | 2   | 3   | 4   |
| 1   | 2   | 5   | 6   |
| 2   | 3   | 4   | 5   |
Identify the superkeys and keys.

Answer:
You may be tempted to say that {C} and {D} are keys and superkeys since they have unique values and thus can be used to identify the other entries based on their value. They are also minimal, satisfying the key definition.

However, note that keys and superkeys are not reliant on us observing the data - we could always add a new entry and make C and D not keys & superkeys anymore. Rather, keys and superkeys are reliant on us as a programming, designing the attributes of C and D as keys/superkeys and inserting new data based on that model so the key properties don't get violated.

Thus, we can **never** determine the keys and superkeys just by looking at the data. (so this was a trick question)

However, by looking at the data, we can determine which attributes are **not** keys/superkeys, like A and B

Also in the relational data model (not SQL tho), you CANNOT have two of the same rows

**Data Independence**
The relational data model provides a logical view of the data, and hides the physical representation of data. This has the advantage of hiding low-level details which allows for design/optimizations without affecting high level user experience.

Two levels of data independence:
- Logical data independence: protects views from changes in logical (conceptual) structure of data
	- okay to change which tables you have if all views can be defined to support retrieval and modification operations correctly
	- something could be a table today and be a view tmr
	- able to change attribute names or add new attributes freely
	- ex: going from Prof(profid, name, dept, salary) and splitting it into ProfPrivate(prodid, salary) and ProfPublic(profid, name, dept) and having smt based on the profpublic relation now instead of the prof relation but still having your views unaffected. that's logical data independence
- Physical data independence: protects conceptual schema from changes in physical structure of data
	- okay to change physical storage of tables like through B-trees or hash, etc.
	- like for example, a relation could be stored as a sorted file by id or stored as an unsorted file with a B+ tree index on the id. physical data independence protects the schema from these physical structure changes

![[Pasted image 20250407020650.png]]


## Review Questions (!!)
- If $D_1$ has $n_1$ elements and $D_2$ has $n_2$ elements, then how many elements are there in $D_1 \times D_2$?
	- $n_1 * n_2$
- If $D_i$ has $n_i$ elements, then how many elements are there in the Cartesian Product $D_1 \times … \times D_k$?
	- $n_1 * n_2 * ... * n_k$
- If $D_1$ has $n_1$ elements and $D_2$ has $n_2$ elements, then how many relation instances can one construct from $D_1 \times D_2$?
	- $2^{n_1 * n_2}$ (cardinality of power set)
- If $D_i$ has $n_i$ elements, then how many relation instances can one construct from $D_1 \times … \times D_k$?
	- $2^{n_1 * n_2 * ... * n_k}$ 
- By looking at a bunch of instances of a relation schema, can we determine whether or not a set of attributes is a key?
	- No, we can't determine that a set of attributes is a key but we can determine that a set of attributes is **not** a key.
- **REMEMBER** (!!) that you can never ever determine which keys are a superkey or key based on looking at a table, you can only determine what is **NOT** a key/superkey!!


