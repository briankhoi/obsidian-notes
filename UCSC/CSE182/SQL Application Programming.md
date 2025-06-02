Often times, we use programs written in other languages that interact with a DB using SQL due to SQL's limited expressibility (no conditional branching, loops, etc.)

**Application Programming Approach: Stored Procedures and Functions**
In this approach, code in a specialized stored procedure language is stored in the DB and executed within the DB when the procedure is called.

This is done using Persistent Stored Modules (PSM) which store procedures as database schema elements, which are then executed within the DB. It is a mixture of conventional PL constructs like branching and loops, and SQL.
- You can't do everything in PSM since PSM execution is in the DB

For PostgreSQL, there is an alternative to PSM called PL/pgSQL which has stored procedures and functions which can be executed from psql. It is what we'll be learning in this course.
- PL/pgSQL has different ways to handle "not found" for loops and different syntax to create stored procedures and functions compared to PSM

**PL/pgSQL CREATE FUNCTION**
![[Pasted image 20250601175306.png]]
A function can take zero or more parameters, passed as a mode-name-type triple, where type signifies the SQL type.

<u>Mode Types</u>:
- IN: value can be read but not updated (default)
- OUT: no initial value, can be updated. caller gets the updated value
- INOUT: value can be updated and caller gets the updated value

Example:
INOUT counter INTEGER

**DECLARE Local Variables**
Structure:
`DECLARE <name> <type> [:= <initial value>];`

The DECLARE statement can be followed by any number of `<name> <type>;` pairs. If there's no initial value for a local variable, then it gets assigned NULL.

**Assignment**
Structure:
`<variable> := <expression>;`

Example: `b := 'Bud'`

**IF Statements**
Structure:
```
IF <condition> THEN
<statement(s)>
ELSIF <condition> THEN
<statement(s)>
ELSE
ENDIF
```

Example:
![[Pasted image 20250601184355.png]]
![[Pasted image 20250601184406.png]]

**CASE Statement**
Similar to match-case or switch-case in other languages:
![[Pasted image 20250601184633.png]]Only the statements for the first expression value matched are executed.

Example:
Scenario is based on previous example scenario (IF Statement section).
![[Pasted image 20250601184718.png]]

**Loops**
Structure:
```
<loop name>: LOOP
	<statements>
	END LOOP;
```

You can also exit from a loop by:
`EXIT <loop name> WHEN <condition>;`
where condition is a boolean expression and loop exits if that condition is true. Exits without a loop name exits the innermost loop.

You can also use the CONTINUE statement to continue execution of the loop at the start (use in same manner as EXIT with loop name and condition values too).

Other loop forms (can use CONTINUE and EXIT in them too):
![[Pasted image 20250601185014.png]]
- \<range> could be something like 1...10

**Queries**
Generally, SELECT-FROM-WHERE queries are not permitted in Stored Procedure Languages like PL/pgSQL, so there are three ways to get the results of a query:
1. Queries which produce one scalar value can be the expression in an assignment. 
2. Queries having single row result: SELECT...INTO
3. Queries having multiple rows result: Cursors

Example: Assignment
Using local variable p and Sells(bar, beer, price), we can get the price Joe charges for Bud by using an assignment:
```SQL
p := ( SELECT price FROM Sells
 WHERE bar = 'Joe''s Bar'
 AND beer = 'Bu
```

Example 2: SELECT...INTO
You can get the value of a query that returns one row by placing INTO \<variable> after the SELECT clause.
```SQL
SELECT price INTO p
 FROM Sells
WHERE bar = 'Joe''s Bar'
 AND beer = 'Bud';

-- you can also do it with multiple attributes!
SELECT price, quantity INTO p, q
 FROM Sells
WHERE bar = 'Joe''s Bar'
 AND beer = 'Bud';
```

**Cursors**
A cursor is a tuple-variable that ranges over all tuples in the result of some query.

Declaration: `DECLARE c CURSOR FOR <query>`
Initialization: `OPEN c;`
- Evaluates the declaration statement of the cursor and sets the cursor to point at the first tuple of the query result
Fetching: `FETCH FROM c INTO x_1, x_2, ..., x_n`
- After initialization of the cursor, we fetch the contents of each tuple one by one by iterating over the results with our cursor (pointer)
- The x's are a list of variables, one for each attribute in the SELECT clause of the query that's in the declaration of $c$
- After FETCH is executed, the cursor is moved automatically and points to the next tuple in the result of the query
Cleanup: `CLOSE c;`

<u>Cursor Loops</u>
The usual way to use a cursor is to create a loop fetching all tuples one by one in each iteration and doing something with the fetched tuples:
![[Pasted image 20250601190224.png]]

To break a loop, you can use a special variable named `FOUND` (it's a global variable that we do not define) to check if FETCH returned a tuple, and then exit when no more tuples are found (meaning your cursor has iterated through all the tuples in the result already).

The syntax for this is `EXIT WHEN NOT FOUND;`
![[Pasted image 20250601190405.png]]

You can also use for loops like this where x1 represents a single attribute in cursor c that was fetched.
![[Pasted image 20250601190526.png]]

**PL/pgSQL PROCEDURE**
![[Pasted image 20250601190601.png]]
- No RETURN type
- Parameters cannot be OUT but can be IN or INOUT

Example:
Write a procedure JoeMenu that takes two arguments b and p, and adds a tuple to Sells(bar, beer, price) that has bar = 'Joe''s Bar', beer = b, and price = p so that Joe can use it to add to his menu more easily.

![[Pasted image 20250601190723.png]]

**Invoking Stored Procedures and Functions**
Stored Procedures are invoked using `CALL`, like 
`CALL JoeMenu('Moosedrool', 5.00);`.

Stored functions can use SQL expressions wherever a value of its return type could appear: `SELECT name, address FROM Bars WHERE Rate(name) = ‘popular’;`

Stored Procedures can execute transactions but stored functions can't. However, stored functions can be invoked within transactions.

**Using a pgsql File**
You write code for stored functions or procedures in a file such as xxx.pgsql. Afterwards, you can execute it with `\i xxx.pgsql` from the pql terminal and invoke a stored function by executing something like `SELECT myStoredFunction(5);`. The stored function can also be used in a SQL statement anywhere the return type of the stored function can be used

<u>Debugging</u>
PL/pgSQL uses the `RAISE` keyword to show information and errors. `RAISE NOTICE` displays information message and continues execution while `RAISE EXECEPTION` raises an error and does not continue execution.

Example:
```SQL
RAISE NOTICE 'Calling CreateJob for %', job_id; RAISE NOTICE 'myInteger parameter is %', CAST( myInteger AS CHAR(10) ); RAISE EXCEPTION 'Person with first name % and last name % not found', firstName, lastName;
```

<u>Statistics</u>
After executing an UPDATE, DELETE, or INSERT statement in PL/pgSQL, you can find out how many tuples were modified by saying: 
`GET DIAGNOSTICS integer_var = ROW_COUNT`
This sets the variable integer_var to the number of rows affected by the previous SQL statement. ROW_COUNT serves as a special, already defined global variable.

**PL/pgSQL Example Function**
```SQL
CREATE OR REPLACE FUNCTION
fireSomePlayersFunction(maxFired INTEGER)
RETURNS INTEGER AS $$


    DECLARE
        numFired INTEGER;     /* Number actually fired, the value returned */
        thePlayerID INTEGER;  /* The player to be fired */

    DECLARE firingCursor CURSOR FOR
            SELECT p.personID
            FROM Persons p, Players play, GamePlayers gp
            WHERE p.personID = play.playerID
              AND play.playerID = gp.playerID
              AND play.rating = 'L'
              AND play.teamID IS NOT NULL
            GROUP BY p.personID
            HAVING SUM(gp.minutesPlayed) > 60
            ORDER BY p.salary DESC;

    BEGIN

        -- Input Validation
        IF maxFired <= 0 THEN
            RETURN -1;          /* Illegal value of maxFired */
            END IF;

        numFired := 0;

        OPEN firingCursor;

        LOOP

            FETCH firingCursor INTO thePlayerID;

            -- Exit if there are no more records for firingCursor,
            -- or when we already have performed maxFired firings.
            EXIT WHEN NOT FOUND OR numFired >= maxFired;

            UPDATE Players
            SET teamID = NULL
            WHERE playerID = thePlayerID;

            numFired := numFired + 1;

        END LOOP;
        CLOSE firingCursor;

        RETURN numFired;

    END

$$ LANGUAGE plpgsql;
```


**Application Programming Approach 2: SQL Statements Are Embedded in a Host Language (Like C)**

**Application Programming Approach 3: Connection Tools/Libraries Are Used to Allow a Conventional Language to Access a DB**
We use psycopg2, which is an adapter for Python access to PostgreSQL.

<u>Approach 2 vs. Approach 3</u>
![[Pasted image 20250601200740.png]]

**Three-Tiered Architecture**
A common environment for using a DB has three tiers of processors: web servers (interact with users), application servers (execute business logic), DB servers.

**Data Structures**
Languages such as C and Python connect to the DB using data structures of the following types:
![[Pasted image 20250601201019.png]]

**psycopg2**
psycopg2 is an adapter that allows Python programs to interact with a PostgreSQL DB

Example:
![[Pasted image 20250601201251.png]]

**Making a Connection**
![[Pasted image 20250601201309.png]]

**Evaluating a SELECT Statement**
<u>For Just One Row</u>
![[Pasted image 20250601201420.png]]

<u>For All Rows</u>
![[Pasted image 20250601201443.png]]

**Executing an UPDATE Statement with Parameters**
There are three ways to do this in pyscopg2, which are also compatible with other statements with parameters like SELECT.

Given the scenario: For the Beers(name, manf) relation, and Python variables oldManf and newManf, we want to UPDATE all the tuples in which manf equals oldManf, setting manf equal to newManf.

<u>Way #1: Construct the full UPDATE statement</u>
![[Pasted image 20250601203634.png]]

<u>Way #2: Provide parameterized SQL statements as a string and supply the parameters</u>
![[Pasted image 20250601203754.png]]

<u>Way #3: Construct a string with the SQL statement, store it in a variable and execute it with parameters</u>
![[Pasted image 20250601203728.png]]

**psycopg2/Python Stored Functions and Procedures**
Call a stored procedure using `CALL` and execute it using cursor.execute()
`myCursor.execute( "CALL JoeMenu(%s, %s);", (theBeer, thePrice) )`

You can use a Stored Function in a SQL statement anywhere that you can use a value of that type, so you can execute a SQL statement that uses a Stored Function using cursor.execute()
`myCursor.execute( "SELECT Rate(%s);", (theBar, ) )` where RATE is a previously defined stored function

**SQL Injection**
SQL Injection is when you construct a SQL statement based on an input value provided from a user, and that input value is malicious/not what you expect.

For example, if you have:
`stmt = “UPDATE Emp SET salary = salary + 100 WHERE empID =” + user_input`
The user can input `12345; DELETE FROM Emp;`, and all of a sudden your Emp table is deleted without you wanting it.

To prevent SQL injection, you can:
- Check that the types of user parameters match types that are expected
	- does not work if expected type is a string
	- you can check for things like user input corresponds to an employee in a table or something
- Separate parameters from SQL command rather than constructing the SQL statement using user input
	- do UPDATE in psycopg2 in Ways #2 and #3, not Way #1!

