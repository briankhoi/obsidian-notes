## Introduction
Elsa is a programming language for lambda calculus where we write programs and expressions, and have Elsa verify if our work is valid or not (checks for lambda calculus correctness).

When running an Elsa program, the compiler gives an error if there's an invalid reduction/series of steps in the calculus and OK if everything is good.

Also note that \f -> is the same as f (

**Notation**
- =b> 
	- beta step
- =d> 
	- d step
	- need to take after identity keyword and before beta step
	- reducing an expression using the =d> like "identity five" and then d-step is called a reduction step or redux
- let (varname)
	- keyword for defining a variable
- \--
	- for comments
- =\*> 
	- take beta steps on this expression until you can't anymore
	- any way to get from step a to step b
- =a>
	- rename variables/function formal defintion
- ~>
	- check that a reduction works - auto reduces the rest of exp
	- reduce to normal form

```python
# in this example, we use =b> to indicate to Elsa that we're taking a beta step
eval example_1 :
	(\x -> x) (\y -> y)
	=b> \y -> y # (takes a beta step)

# in this example, we use the "let" keyword to define a statement so we don't have to keep repeating it over and over

# also note that after using the identity variable, we also have to expand out the identity using a d-step which expands it (in that case why use identity at all ?)
# - note that we only have to expand the function/left side, argument does not have to be expanded

let identity = \x -> x

eval example_2 : 
	identity (\y -> y)
	=d> (\x -> x) (\y -> y)
	=b> \y -> y

eval example_3 : 
	((\x -> (\y -> x)) five) six
	=b> (\y -> five) six
	=b> five

eval example_4 :
	identity five
	=d> (\x -> x) five
	=b> five

eval example_5 : 
	(\f -> f(\x -> x)) (\y -> y)
	=b> (\y -> y) (\x -> x)
	=b> \x -> x

eval example_6 :
	(\f -> f(\x -> x)) (\y -> y)
	=*> \x -> x

let rainbow = \x -> three

eval example_7 :
	rainbow identity
	=d> (\x -> three) identity
	=b> three

# can also do
eval example_8:
	rainbow identity
	=*> three 
```

## Encoding Values
**Booleans**
```python
# in Elsa, boolean values are encoded as such:
let TRUE = \x y -> x
let FALSE = \x y -> y

# ITE stands for "if ... then ... else" - figure out how?
let ITE = \b x y -> b x y
# keep in mind ITE is just shorthand for \b -> (\x y -> b x y)
	let NOT = \b x y -> b y x

eval if_true : 
	ITE TRUE rainbow sprinkles
	=d> (\b x y -> b x y) TRUE rainbow sprinkles
	=b> (\x -> y -> TRUE x y) rainbow sprinkles
	=b> (\y -> TRUE rainbow y) sprinkles
	=b> TRUE rainbow sprinkles
	=d> (\x y -> x) rainbow sprinkles
	=b> (\y -> rainbow) sprinkles
	=b> rainbow

eval if_false :
	ITE FALSE rainbow sprinkles
	=d> ITE (\x y -> y) rainbow sprinkles
	=d> (\b x y -> b x y) (x y -> y) rainbow sprinkles
	=b> (\x y -> (\x y -> y) x y) rainbow sprinkles
	=b> (\x y -> (\y -> y) y) rainbow sprinkles
	=b> (\x y -> y) rainbow sprinkles
	=b> (\y -> y) sprinkles
	=b> sprinkles

eval not_true : 
	NOT TRUE
	=d> (\b x y -> b y x) TRUE
	=b> \x y -> TRUE y x
	=d> \x y -> (\x y -> x) y x
	# you would think this is the next step, but it's NOT - there's different instances of x and y, the x in the parentheses is different from the instance of x, the argument
	=b> \x y -> (\y -> y) x
	# to combat this, we rename the function itself to separate the instances
	=a> \x y -> (\p y -> p) y x
	=b> \x y -> (\q -> y) x
	=b> \x y -> y
	# we can also use a d-step to capture a function and rename stuff, not just expand
	=d> FALSE
```

**Church Numerals**
```python
let ZERO = \f x -> x
let ONE = \f x -> f x
let TWO = \f x -> f (f x)
let THREE = \f x -> f (f (f x))

# incrementing numbers: to increment a number, we use the following function
let INCR = \n -> (\f x -> f (n f x))

eval incr_example :
	INCR ZERO 
	=d> (\n -> (\f x -> f (n f x))) ZERO
	=b> \f x -> f (ZERO f x)
	=d> \f x -> f ((\f x -> x) f x)
	=b> \f x -> f ((\x -> x) x)
	=b> \f x -> f x
	=d> ONE

# adding numbers
let ADD = \n m -> n INCR m

eval add_two_one :
  ADD TWO ONE
  =d> (\n m -> n INCR m) TWO ONE
  =b> (\m -> TWO INCR m) ONE
  =b> TWO INCR ONE
  =d> (\f x -> f (f x)) INCR ONE
  =b> (\x -> INCR (INCR x)) ONE
  =b> INCR (INCR ONE)
  =d> (\n -> (\f x -> f (n f x))) (INCR ONE)
  =b> \f x -> f ((INCR ONE) f x)
  =d> \f x -> f (((\n -> (\f x -> f (n f x))) ONE) f x)
  =b> \f x -> f ((\f x -> f (ONE f x)) f x)
  =b> \f x -> f ((\x -> f (ONE f x)) x)
  =b> \f x -> f (f (ONE f x))
  =d> \f x -> f (f ((\f x -> f x) f x))
  =b> \f x -> f (f ((\x -> f x) x))
  =b> \f x -> f (f (f x))
  =d> THREE

eval add_two_one_abridged :
  ADD TWO ONE
  =d> (\n m -> n INCR m) TWO ONE
  =b> (\m -> TWO INCR m) ONE
  =b> TWO INCR ONE
  =d> (\f x -> f (f x)) INCR ONE
  =b> (\x -> INCR (INCR x)) ONE
  =b> INCR (INCR ONE)
  =d> (\n -> (\f x -> f (n f x))) (INCR ONE)
  =b> \f x -> f ((INCR ONE) f x)
  =~> THREE
```

**Pairs and Alpha Renaming**
```python
# Given x and y, create a pair (x, y)
let PAIR = \x y -> (\b -> ITE b x y)

# Given a pair (x, y), get back x
let FST = \p -> p TRUE

# Given a pair (x, y), get back y
let SND = \p -> p FALSE

# What does this do?
# It takes a pair and returns the second element of the pair
# if the first element evaluates to TRUE;
# otherwise, it returns FALSE.
let PROGRAM = \p -> ITE (FST p) (SND p) FALSE

eval alpha_example :
  (\x -> (\y -> x)) y
  =a> (\x -> (\z -> x)) y
  =b> \z -> y

# I could've picked something else too:

eval alpha_example_2 :
  (\x -> (\y -> x)) y
  =a> (\x -> (\q -> x)) y
  =b> \q -> y

eval alpha_equivalent :
  \z -> y
  =a> \q -> y

eval pair_of_cats_fst :
  FST (PAIR rainbow sprinkles)
  =d> (\p -> p TRUE) (PAIR rainbow sprinkles)
  =b> (PAIR rainbow sprinkles) TRUE
  =d> ((\x y -> (\b -> ITE b x y)) rainbow sprinkles) TRUE
  =b> ((\y -> (\b -> ITE b rainbow y)) sprinkles) TRUE
  =b> (\b -> ITE b rainbow sprinkles) TRUE
  =b> ITE TRUE rainbow sprinkles
  =~> rainbow

eval pair_of_cats_snd :
  SND (PAIR rainbow sprinkles)
  =d> (\p -> p FALSE) (PAIR rainbow sprinkles)
  =b> (PAIR rainbow sprinkles) FALSE
  =d> ((\x y -> (\b -> ITE b x y)) rainbow sprinkles) FALSE
  =b> ((\y -> (\b -> ITE b rainbow y)) sprinkles) FALSE
  =b> (\b -> ITE b rainbow sprinkles) FALSE
  =b> ITE FALSE rainbow sprinkles
  =~> sprinkles

eval quiz_question :
  SND (FST (PAIR (PAIR TRUE FALSE) TRUE))
  =~> FALSE
```

**Triangular Numbers**
```python

"""
Computing *triangular* numbers

The triangular number T(n) is the sum of all natural numbers up to n.

T(0) = 0
T(1) = 1 + 0 = 1
T(2) = 2 + 1 + 0 = 3
T(3) = 3 + 2 + 1 + 0 = 6

T(0) = 0  -- base case
T(n) = n + T(n-1) -- inductive case


We *wish* we could write this...but we can't quite do this.
let TRI = \n -> ITE (ISZ n)
                     ZERO
                     (ADD n (TRI (DECR n)))
 # this is because TRI is undefined when we call it recursively in the same line, so we add another variable to let us recurse without explicit self-reference. this is done through the Y variable

We can do this instead:
"""

# Config
# Omega is a function that stays the same no matter how many beta reductions, so evaluating it would lead to infinite =b> (\x -> x x) (\x -> x x) aka =b> OMEGA
let OMEGA = (\x -> x x) (\x -> x x)
# y is a function that repeatedly adds another application of f
"""
--   \f -> (\x -> f (x x)) (\x -> f (x x))
--   =b> \f -> f ((\x -> f (x x)) (\x -> f (x x)))
--   =b> \f -> f (f ((\x -> f (x x)) (\x -> f (x x))))
"""
let Y = \f -> (\x -> f (x x)) (\x -> f (x x))

# Actual Implementation
let TRI1 = \rec -> \n -> ITE (ISZ n) ZERO (ADD n (rec (DECR n)))

let TRI = Y TRI1

"""
In general, the recipe for writing a recursive function is:

- Write the function you *wish* you could write, with a recursive call
- Take an extra argument as the first argument, and replace the name
  of the recursively defined function with that argument
  (e.g., replace a call to TRI with a call to rec in our example)
- Pass that function with an extra argument to `Y`

-- eval omega :
--   (\x -> x x) (\x -> x x)
--   =b> (\x -> x x) (\x -> x x)

-- eval y :
--    \f -> (\x -> f (x x)) (\x -> f (x x))
--    =b> \f -> f ((\x -> f (x x)) (\x -> f (x x)))
--    =b> \f -> f (f ((\x -> f (x x)) (\x -> f (x x))))
--    =b> \f -> f (f (f ((\x -> f (x x)) (\x -> f (x x)))))
"""

eval tri_zero :
  TRI ZERO =~> ZERO

eval tri_one :
  TRI ONE 
  =d> (Y TRI1) ONE
  =d> ((\f -> (\x -> f (x x)) (\x -> f (x x))) TRI1) ONE
  =b> (\x -> TRI1 (x x)) (\x -> TRI1 (x x)) ONE
  =b> TRI1 ((\x -> TRI1 (x x)) (\x -> TRI1 (x x))) ONE
  =d> (\rec -> \n -> ITE (ISZ n) ZERO (ADD n (rec (DECR n)))) ((\x -> TRI1 (x x)) (\x -> TRI1 (x x))) ONE
  =b> (\n -> ITE (ISZ n) ZERO (ADD n (((\x -> TRI1 (x x)) (\x -> TRI1 (x x))) (DECR n)))) ONE
  =b> ITE (ISZ ONE) ZERO (ADD ONE (((\x -> TRI1 (x x)) (\x -> TRI1 (x x))) (DECR ONE)))

# The above will eventually crunch down to `ADD ONE ZERO`, which is...
  =~> ONE


eval tri_two :
  TRI TWO =~> THREE

eval tri_three :
  TRI THREE =~> SIX
```