## Introduction
Lambda Calculus is a programming language where everything is a function. Functions can return functions and take functions as arguments.

The simplest function in lambda calculus is called the identity function, where it outputs the same value as its input:
$$ \lambda x.x $$ In this function:
- $\lambda x$ is called a binder where x inside the binder is called the formal parameter
- The . is a delimiter to separate the binder from the function body
- The $x$ after the delimiter (on the right) is called the function body

In other programming languages, this is represented as:
- \ $x->x$ (in Elsa, the language we'll use in class)
- lambda x: x (python)

**Here is another function:** $\lambda z.(\lambda x.x)$
This function basically ignores its input and returns the identity function of another variable

**To call a function in lambda calculus,** we don't put the parameters around the argument in a function call, but rather just around the function itself.
- (\x -> x) "rainbow" (this function calls the identity function with the argument "rainbow")

If you wrap the parentheses around the argument, like \x->x "rainbow", then it would be misinterpreted as a function whose body is "x rainbow"

**Here is an example of a function taking another function as an input:**
In this example, we take a function f as an input and applies it to the identity function.
1. Starting with the given function: **\f -> f(\x -> x)**
2. We now call it with the identity function: (\f -> f (\x -> x)) \y -> y
To evaluate this expression, we plug in the argument in place of the occurrences of the formal parameter in the formal body

Applied to the example, this gives us:
3. (\y -> y) \x -> x
This is basically an identity function called with another identity function as its input. Thus, this simplifies to:
4. \x -> x 
## The Substitution Rule (B-rule)
The substitution rule tells us how to apply a function to an argument. Essentially, it says you take the body of the function $e_1$, and replace every free occurrence of x within the body with the expression $e_2$, which is called the beta step.
(\x -> $e_1$ ) $e_2 \rightarrow$  $_\beta \ e_1 \ [x := e_2]$ 

**Quiz question:** Evaluate (\x -> x) y
Applying the substitution rule, we have
(\x -> x) y $\rightarrow \ _\beta \ x[x := y]$ 
So we replace each x within the body with y, giving us y as the final answer.
## Functions with Multiple Arguments
To a write a function with multiple arguments, consider the example below:
(\x -> (\y -> x))
Calling this function, we have:
((\x -> (\y -> x)) 5) 6
Then, use a beta step and plug in 5 where x is in function body, so we have $_\beta$  \y -> 5 so now we have a constant function that returns 5. Then, when plugging it 6, it doesn't matter since y is not in the function body so the result is still 5.

In this function, we were able to call two arguments, x and y, through passing them in one at a time.

**Left Association**
Looking at the initial function from the previous example,
\x -> (\y -> x)
We can also write it as
\x y -> x
where the function body is the innermost function body with respect to the previous inputs.
So now, the function call becomes:
((\x y -> x) 5) 6
which is also equivalent to
(\x y -> x) 5 6

Thus, in lambda calculus, function application is <u>left-associative</u> 
meaning that to evaluate **f g x**, we first apply f to g, then apply the result of that to x.
**$\therefore$ f g x is shorthand for (f g) x**

However, if we wanted to apply g to x first, then apply f to the result, we would write: f (g x)

**Church Numerals**
Church numerals are our way of encoding numbers into lambda calculus, where everything is a function.

The basis is defining a function that takes two parameters, f and x. Then, the number of times f is applied to x in the function body represents our number.

We define the following numbers:
- Zero: \f x -> x
- One: \f x -> f x
- Two: \f x -> f (f x)
- Three: \f x -> f (f ( f x))


*insert monday apr 7 missed out on first 10 mins of lecture*

e ::= x variable
\x -> e( fn definition, lambda abstractions
e1, e2 (fn calls

notational conventions
body of lambda abstraction extends as far to right as psosible
so \x -> m n means  \x -> (m n ) not (\x -> m) n))
fn application is left associative
instead of \x -> (\y -> (\z - > e))

we can do with out the ()
beta reule is a redux reduction step
redux (reducibe exp is anything you can apply beta rule to)


**Normal Form**
A lambda calculus expression in <u>normal form</u> is one that has no redexes.

Example:
(\x y -> (\z -> z) x) rainbow sprinkles
=b> (\x y -> x) rainbow sprinkles
=b> (\y -> rainbow) sprinkles
=b> rainbow

or, we could have done:
(\x y -> (\z -> z) x) rainbow sprinkles
=b> (\y -> (\z -> z) rainbow) sprinkles
=b> (\z -> z) rainbow
=b> rainbow

Church-Rosser theorem: if you can take multiple b-steps in a specific step, then any series of b-steps you take will lead you to the same answer.

**Variable Scope**
A <u>binding</u> is an association (name, entity) of a name to some entity.

The <u>scope</u> of a binding in a program is the part of the program where the name can be used to refer to the entity.

For example, in \x -> e, e is the scope of the binding from the formal parameter x to whatever argument is passed to this function when its called. We say that any parameter of x in e is bound by \x.

So in:
- \x -> x
- \x -> (\y -> x)
both x's in the body are bound occurrences of x.

An occurrence of a variable is <u>free</u> if it is not bound.

For instance, 
- In x y both variables are free
- In \y -> x y, x is free y is bound by \y
	- x y is the entire body not just x -> if y was an argument you would declare parentheses around the function ?
- (\x y -> y), x is free, y is bound
- (\x y -> x y) x, x occurs both free (outside x) and bound (x in the body of the parentheses), y occurs bound

The generic lambda calculus expression can be modeled as:
e := x | \x -> e | $e_1$ $e_2$

Let FV(e) be the set of variables that occur free in e.
- FV(x) = {x}
- FV(\x -> e) = FV(e) - {x}
- FV($e_1 \ e_2$) = FV($e_1$) $\cup$ FV($e_2$)

An expression that has no free variables is <u>closed</u>. A closed expression is also known as a <u>combinator</u>, i.e. \x -> x.

Question: What is the set of free variables in (\x -> x (\y -> z)) (x y)?
- We know that the set of free variables with the form FV($e_1 \ e_2$) is given by FV($e_1$) $\cup$ FV($e_2$). So the FVs of left expression are z, and the FVs of the right expression are x and y, so it's {x, y, z}
- y is not a FV in the left expression because it is a formal parameter \y ??

**Revisiting the $\beta$-Rule**
Given the expression (\x -> (\y -> x)) y, when we evaluate it with the beta rule we get: \y -> y which is NOT the correct solution, since the y in the body is different from the formal parameter as it was in the function arguments instead. Formally, the statement is wrong because y occurred free in the argument and got captured by the \y binder in the body of the function.

So, if we ever use the substitution operation and have a free variable being captured by a binder, we need to <u>rename</u> the formal parameters first (with =a> in Elsa) so that we don't get mixed up and confused between the two variables.

 Now for the old definition of the beta rule: (\x -> $e_1$) $e_2$ -> $_\beta \ e_1[x := e_2]$, how do we redefine the body of $e_1[x := e_2]$ so that it avoids variable capture? We break it down into a few cases:
- If $e_1$ is a variable and it's the same as the formal parameter in the binder (so like $e_1 == x$ in cases like \x -> x where formal parameter and binder are the same), then we have
	- $x \ [x: = e_2] = e_2$ 
	- because if body is just x, and you replace all instances with $e_2$, it's just $e_2$
- If $e_1$ is a variable and it's <u>not</u> the same as the formal parameter in the binder ($e_1 == y$ where y != x)
	- $y \ [x:=e_2] = y$
	- if body is not x/does not have formal parameter, you can't replace all instances of it since there are no instances, so it's just y
- If $e_1$ is an application of e' e'', so like (e' e'') \[x := $e_2$]
	- e'\[x := $e_2$] e''\[x := $e_2$]
	- basically just apply the replacement to each part
- If $e_1$ is a function with x as its formal parameter ($e_1$ == \x -> e')
	- (\x -> e') \[x := $e_2$] = \x -> e'
	- this is talking about cases like \x -> (\x -> e'), since the \x inside $e_1$ is different from the formal parameter outside \x, you can't replace it with $e_2$ since if there's an x inside of e', it'll correspond to the inside \x NOT the outside \x
	- so like (\x -> (\x -> TRUE)) 1 simplifies to \x -> TRUE after the beta step
- If $e_1$ is a function with something other than x as its formal parameter like ($e_1$ == \y -> e' where y != x)
	- (\y -> e') \[x := $e_2$]
	- since we know x and y are different variables and don't overlap, if there's an x in $e_2$ it for sure corresponds to the outside formal parameter \x so we do normal substitution
	- Two Cases:
		- If y is <u>not</u> in FV($e_2$)
			- (\y -> e') \[x := e_2] = \y -> e'\[x := $e_2$]
			- if y is not in the free variables of $e_2$, it means all y's are bounded and thus we can directly apply the substitution since we know that \y only affects the variable y within $e_2$ not any y that can arise from substituting x with $e_2$
		- If y <u>is</u> in FV($e_2$)
			- Substitution is undefined and we have to do a renaming step before substitution
			- for example, take the statement: (\x -> (\y -> x)) y where we can see y is a free variable. simplifying this to \y -> y would be wrong and is called **variable capture** where one variable captures the value of another when it's not intended, and this can for the worse change the meaning of the intended expression
				- thus, we rename the inner \y formal parameter to z with an alpha step =a> (\x -> (\z -> x)) y, so it now re-evaluates to \z -> y
				- also notice how \x ->x and \y -> y are $\alpha$-equivalent!
				- This is known as an $\alpha$-step or $\alpha$-renaming
				- The process of 

**Pairs**
Pairs are two element values like PAIR ONE TWO. Using pairs, we can:
- Access the first and second elements individually
- Take two things and construct a pair

**Omega Expression**
An omega expression is one with no normal form:
(\x -> x x) (\x -> x x)
We can see that no matter how many times we use a beta step and try to reduce this, it always leads to =b> (\x -> x x) (\x -> x x) forever.