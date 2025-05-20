**Introduction**
```haskell
-- varname :: type (:: used to declare type)
message :: String
-- = used to declare message
message = "Welcome to lecture 8!"
-- we can also write
message :: [Char] -- [Char] is an array of chars which represents a string
message = (\x -> "Welcome to lecture " ++ x ++ "!") "9"
```

**Pattern Matching Example**
```Haskell
{-
-- Let's translate this to Haskell
-- TRI = \n -> ITE (ISZ n)
--                 ZERO
--                 (ADD n (TRI (DECR n)))
-- ({- msg -}) for multi line comments, -- are single line comments 

The triangular number T(n) is the sum of all natural numbers up to n.

T(0) = 0
T(1) = 1 + 0 = 1
T(2) = 2 + 1 + 0 = 3
T(3) = 3 + 2 + 1 + 0 = 6

T(0) = 0  -- base case
T(n) = n + T(n-1) -- inductive case
-}
-- A correct, but non-idiomatic, version of `tri`
-- Int -> Int means the function tri takes one int argument and returns one int argument
tri :: Int -> Int
-- defining the function tri; we use equal signs as a return statement/to return values
tri = \n -> if (n == 0)
               then 0
               else (n + tri (n - 1))

-- Slightly more idiomatic:
tri' :: Int -> Int
tri' n = if (n == 0)
           then 0
           else (n + tri' (n - 1))

-- Actually idiomatic: use pattern matching
tri'' :: Int -> Int
-- this is a pattern matching example: if tri'' gets called with 0, it returns 0
tri'' 0 = 0
tri'' n = n + tri'' (n - 1)

-- [a] is declaring a list where elements can be of any type a; a is a type variable
length' :: [a] -> Int
-- if length' is called with an empty list it returns 0
length' []     = 0
```

**Pattern Matching Example 2 and Appending**
```Haskell
{-
(_:xs) pattern matches any non-empty list
- _ (underscore char) is a wildcard that matches first element of list (its head) but we don't give it a name since we don't need its value 
- : (colon) is "cons" operator which takes an element and prepends it to another list, so like "2 : (1 : []) is [2, 1]"
- xs is the rest of the list aka the entire list (its tail) excluding first element (xs is just a random var name)
so, the way the length function works is that it continuously adds one to the length count and recursively calls itself on the subset of list elements position 2 to the end until we reach an empty list
-}

-- this pattern of (-:xs) is matched for non-empty lists because the : pattern expeects a list that can be deconstructed into a head and a tail. so if we input [1, 2, 3], _ matches 1 while xs matches [2, 3]
-- can't use [a] as pattern match cause that matches a list with exactly one element bounded to the variable a
length' (_:xs) = 1 + length' xs

-- Our version of the built-in append function, `(++)`
-- [a] -> [a] -> [a] means it takes two lists of same type a and returns a list of type a
append :: [a] -> [a] -> [a]
-- we're saying if the first list is empty, just return the second list (represented by the variable xs)
-- also notice how append is taking in two inputs, both separated by a space after the function call
append [] xs = xs
-- essentially, the goal of this list append is to append two lists with all the elements of the first list come before all the elements of the second list
append (y:ys) xs = y : append ys xs

-- like the comments said above, this is a built in operator (++) in haskell

-- duplicateAll: this function duplicateAll takes a list and creates a new list where each element of the original list appears twice in a row
duplicateAll :: [b] -> [b]
duplicateAll []     = []
-- this results in a new list where the head element x is included twice in a row, followed by the result of recursively calling duplicateAll on the rest of the list xs.
-- cons operator (:) prepends an element to front of list and is right associative unlike elsa which is left associative. we can't do x ++ x because ++ is for lists only and x is not a list it's an element. you would have to do [x] ++ [x] but it's not efficient
duplicateAll (x:xs) = x : x : duplicateAll xs

-- The quiz question
-- Given:
f :: [String] -> [String]
f [] = []
f (x:xs) = ["sam"] ++ xs
-- What does the expression f ["mo", "cookie"] evaluate to?
-- Answer: ["sam", "cookie"]
```

```Haskell

-- Pattern guards
safeTri :: Int -> Int
-- pattern guards are the |, so if both conditions are true it evalues to the string
-- you can have multiple pattern guards like safeTri n | (another condition) = smt else in this function
-- make sure you have a case that suits all inputs though, so if we have a case for < 0 and == 1, then haskell would error if we input 5 since we dont have a safeTri n (in the function below we do)
-- however even with n | n <0 this is assuming there's an input. if we call it by itself like just safeTri haskell doesnt know what to print or do so it errors
-- we can also do | otherwise = smt for base case evals
safeTri n | n < 0 = error "Please don't call me with arguments less than 0 kthx"
safeTri n = tri n

-- f :: Int -> String
-- f n | n `mod` 2 == 0 = "the argument was even!"
-- f n | n `mod` 2 == 1 = "the argument was odd!"

infiniteList :: a -> [a]
infiniteList x = x : infiniteList x

{-

How did GHCi know that the type of `infiniteList "sprinkles"` is `[String]`?

Some clues:
- GHCi knows `"sprinkles"` is a `String` because it's a string literal in double quotes.
- GHCi knows `infiniteList` has type `a -> [a]` from the type annotation on the function.
- GHCi knows when a function `f` of type `t1 -> t2` is applied to an argument `x` of type
  `t1`, the entire expression `f x` has `t2` (this is a built-in typing rule)
-- for this specific call of infinitelist a is inferred as a string since we pass in a string
Putting it all together gives us the type `[String]`.

-}

f :: [String] -> [String]
f [] = []
f (x:xs) = ["sam"] ++ xs

lastDigit :: Integer -> Integer
lastDigit n = n `mod` 10

allButLastDigit :: Integer -> Integer
allButLastDigit n = n `div` 10
```

```Haskell

-- CSE114A pets database

-- We can define a type using the `data`
-- keyword:

-- name, owner, type of animal, age
-- - `PetRecord` is the data type
-- - `PetRec` is the data constructor 
--   for data of type PetRecord
-- deriving show is so that custom stuff like PetAge and PetRecord can be displayed properly in a string format in haskell/ghci
data PetRecord = PetRec String String String PetAge
  deriving Show

{-

`PetRecord` is a *product type*.
Product types are great for putting multiple
types together into one type.

Something of type PetRecord is in some sense
an element of the set String x String x String x Int

`PetAge` is a *sum type*.
Sum types are great for choosing among options.

Recursive types:
Think of lists. A list can be:

- an empty list
- an element, together with a list.

-}

-- Combining all three concepts:
-- Let's define our our type for lists of strings.
-- This is a recursive type -- it refers to itself!
-- empty and nonempty and constructors, we separate them with |
data StringList = Empty | NonEmpty String StringList

-- `IntAge` and `UnknownAge` are constructors
-- for values of type `PetAge`.
-- deriving show is applied to the entire definition of pet age and we can just use it like let whiskersAge = UnknownAge
data PetAge = IntAge Int | UnknownAge
  deriving Show

database :: [PetRecord]
database = [PetRec "rainbow" "lindsey" "cat" (IntAge 3),
            PetRec "sprinkles" "lindsey" "cat" (IntAge 4),
            PetRec "nympha" "aj" "cat" (IntAge 13),
            PetRec "larry" "harinand" "cat" (IntAge 4),
            PetRec "bewie" "tori" "cat" UnknownAge,
            PetRec "walter" "tori" "cat" UnknownAge]

-- Take a pets database and an owner's name,
-- and return a list of their pets' names
-- (showing off a `where` clause and multiple pattern guards)
{- 
essentially, we are inputting the database (array of pet records) and parsing them row by row.
(PetRec name o _ _:xs) the stuff before the cons operator is matching each field in a petrecord, while the stuff after (xs) is all the subsequent rows
getPetsByOwner [] _ = [] is edge case for empty, _ is used cause if db is empty we dont care about owner's name so we mark as _

we define a helper fn inside called same which checks if two strings are equivalent or not
same :: String -> String -> Bool
          same s s' = s == s'

we then use this helper function in the pattern guard same o owner to see if o (owner's name stored in db) and owner (owner input param) are equal. if so, we do name : rest which prepends the name of the pet to the rest of the result (essentially we're building a list of all the pets that belong to the owner)

rest is defined in the where clause which recursively calls the function on the rest of the rows in the db with the same owner name

also we don't need to define the same function, we can just do o == owner directly in the guard clause
-}
getPetsByOwner :: [PetRecord] -> String -> [String]
getPetsByOwner [] _ = []
getPetsByOwner (PetRec name o _ _:xs) owner | same o owner = name : rest
                                            | otherwise    = rest
    where rest = getPetsByOwner xs owner
          same :: String -> String -> Bool
          same s s' = s == s'
{- 
Using recursive types, how do you think we should define a Haskell data type for binary trees of `Int`s, where the `Int`s are stored *only at the leaves* of the tree?
-}
-- we do Node Tree Tree instead of Node Tree because Node is the connecting point of the two branches Tree and Tree, so like
{-
    Node 
  /      \
Tree    Tree
-}
data Tree = Leaf Int | Node Tree Tree
  deriving Show

-- In the long run, we want to represent *programs* as trees.
-- What about arithmetic expressions like `3 + 4`?
-- Or `(3 + 4) - 5`, or `(3 + 4) * (2 - (1 * 6))`?

data Expr = Number Int 
          | Sum Expr Expr
          | Diff Expr Expr
          | Prod Expr Expr

-- 3 + 4
expr1 :: Expr
expr1 = Sum (Number 3) (Number 4)

-- (3 + 4) - 5
expr2 :: Expr 
expr2 = Diff (Sum (Number 3) (Number 4)) (Number 5)

-- (3 + 4) * (2 - (1 * 6))
expr3 :: Expr
expr3 = Prod expr1 (Diff (Number 2) (Prod (Number 1) (Number 6)))
-- this is an AST (abstract syntax tree)!
```

```Haskell
database :: PetDB
database =  NonEmpty (PetRec "rainbow" "lindsey" "cat" (IntAge 3))
                     (NonEmpty (PetRec "sprinkles" "lindsey" "cat" (IntAge 4))
                               (NonEmpty (PetRec "nympha" "aj" "cat" (IntAge 13))
                                         (NonEmpty (PetRec "larry" "harinand" "cat" (IntAge 4))
                                                   (NonEmpty (PetRec "bewie" "tori" "cat" UnknownAge)
                                                             (NonEmpty (PetRec "walter" "tori" "cat" UnknownAge) Empty)))))

data PetDB = Empty | NonEmpty PetRecord PetDB

-- Take a pets database and an owner's name,
-- and return a list of their pets' names
getPetsByOwner :: PetDB -> String -> [String]
getPetsByOwner Empty _ = []
getPetsByOwner (NonEmpty (PetRec name owner _ _) rest) ownerName = 
    if owner == ownerName then name : restOfDB else restOfDB
    where restOfDB = getPetsByOwner rest ownerName

-- The (abstract) syntax of our language
-- Syntax: What programs look like
data Expr = Number Int 
          | Sum Expr Expr
          | Diff Expr Expr
          | Prod Expr Expr
          | IfZero Expr Expr Expr
  deriving Show

-- 3 + 4
expr1 :: Expr
expr1 = Sum (Number 3) (Number 4)

-- (3 + 4) - 5
expr2 :: Expr
expr2 = Diff (Sum (Number 3) (Number 4)) (Number 5)

-- (3 + 4) * (2 - (1 * 6))
expr3 :: Expr
expr3 = Prod expr1 (Diff (Number 2) (Prod (Number 1) (Number 6)))
-- this is an AST (abstract syntax tree)!

-- The semantics of our language
-- Semantics: what programs *mean*
-- In this case, what an `Expr` "means" is just what it evaluates to.
-- We could come up with a more sophisticated notion of "meaning", but this'll do for now.

-- interp basically evaluates expressions through its base case of number n
interp :: Expr -> Int
interp (Number n) = n
interp (Sum e1 e2) = interp e1 + interp e2
interp (Diff e1 e2) = interp e1 - interp e2
interp (Prod e1 e2) = interp e1 * interp e2
interp (IfZero e1 e2 e3) = if interp e1 == 0 then interp e2 else interp e3
```

```Haskell
-- add a ! (bang operator) in front of something to make it evaluate not lazily

len :: [a] -> Int
len []       = 0
len (_:rest) = len rest + 1

{-
An oversimplified, but hopefully educational,
visualization of how a call to `len` evaluates:

len [1, 2, 3, 4, 5]
= (len [2, 3, 4, 5]) + 1
= ((len [3, 4, 5]) + 1) + 1
= (((len [4, 5]) + 1) + 1) + 1
= ((((len [5]) + 1) + 1) + 1) + 1
= (((((len []) + 1) + 1) + 1) + 1) + 1
= ((((0 + 1) + 1) + 1) + 1) + 1
= 5

Unless it's called on an empty list, the last thing that `listLength` does before returning is a `+` operation -- it adds `1` together with the result of calling `listLength`. Therefore, `listLength` is _not_ tail-recursive, because the last thing it does before returning is call `+`, rather than making a recursive call to itself.

When executed, non-tail-recursive functions can quickly run out of stack space, because they use space proportional to the size of their input.

Under the hood, tail-recursive functions can be efficiently implemented in such a way that each recursive call to a function can reuse the same _stack frame_ as the previous call. Unlike non-tail-recursive functions, which can quickly run out of stack space, tail-recursive functions only require a small constant amount of stack space to run.

The following alternate implementation of `listLength`, `listLengthAcc`, is tail-recursive. It uses an extra _accumulator_ argument to accumulate the running total, so that there's no need to do addition outside of the recursive call.
-}


-- A tail-recursive version of len'
-- That means that every recursive call that this function makes
-- is in *tail position*.
-- Tail position means it's the last thing the function does.
len' :: [a] -> Int
len' ls = len'' ls 0
  where len'' :: [a] -> Int -> Int
        len'' []       !acc = acc
        len'' (_:rest) !acc = len'' rest (acc + 1)

{-
An oversimplified, but also hopefully educational, visualization

len' [1, 2, 3, 4, 5] 0
= len' [2, 3, 4, 5] 1
= len' [3, 4, 5] 2
= len' [4, 5] 3
= len' [5] 4
= len' [] 5
= 5

There's nothing waiting around to be evaluated on the outside
of the recursive call.

More truthful version: 

Because of lazy evaluation, it's more like this:

len' [1, 2, 3, 4, 5] 0
= len' [2, 3, 4, 5] (0 + 1)
= len' [3, 4, 5] ((0 + 1) + 1)
= len' [4, 5] (((0 + 1) + 1) + 1)
= len' [5] ((((0 + 1) + 1) + 1) + 1)
= len' [] (((((0 + 1) + 1) + 1) + 1) + 1)
= 5

It turns out that the accumulator argument is
evaluated lazily, obviates the benefits of tail recursion.

What do?  Make the accumulator argument evaluated strictly.

-}


-- Laziness is both friend and foe

-- Here's an example where it's good:
-- Take a list and return the fifth element
fifth :: [a] -> a
fifth (_:_:_:_:x:_) = x -- Matches any list of 5 or more elements
fifth _             = error "no fifth element"
-- laziness works here because haskell only evaluates elements 1-5 (1-4 are needed to traverse list to get to element 5) while rest of list is not useed so it's not stored in memory

-- The Maybe type

-- data Maybe a = Nothing | Just a

-- Fun fact: Maybe in Haskell is the same as Option in Rust

-- Functions that take functions as arguments

-- Take a predicate and an argument,
-- and apply the predicate to the argument, 
-- returning the result
applyPredicate :: (a -> Bool) -> a -> Bool
applyPredicate pred a = pred a


-- Find the first element in a list that satisfies a given predicate
find :: (a -> Bool) -> [a] -> Maybe a
find predicate [] = Nothing
find predicate (x:xs) = if predicate x then Just x else find predicate xs

{-
find (\x -> x `mod` 2 == 0) [1, 3, 5]
find (\x -> x `mod` 2 == 0) [3, 5]
find (\x -> x `mod` 2 == 0) [5]
find (\x -> x `mod` 2 == 0) []
Nothing

find (\x -> x == 5) [1, 2, 3, 4, 5, 6]
find (\x -> x == 5) [2, 3, 4, 5, 6]
find (\x -> x == 5) [3, 4, 5, 6]
find (\x -> x == 5) [4, 5, 6]
find (\x -> x == 5) [5, 6]
Just 5

-}


-- Direct-style implementation
fact :: Int -> Int
fact 0 = 1
fact n = n * fact (n - 1)

-- Accumulator-passing style implementation
-- This implementation is tail-recursive
-- because the recursive call is in tail position
factAcc :: Int -> Int
factAcc n = helper n 1
  where helper :: Int -> Int -> Int
        helper 0 acc = acc
        helper n acc = helper (n - 1) (acc * n)

-- In accumulator-passing style we passed around an extra data structure
-- to accumulate the result.

-- In continuation-passing style we will pass around an extra *function*
-- to accumulate the computation we want to do!

-- The argument of type `Int -> Int` is our continuation argument.
factCPS :: Int -> (Int -> Int) -> Int 
factCPS 0 k = k 1
factCPS n k = factCPS (n - 1) (\v -> k (n * v))

{-
Let's walk through a call to factCPS

factCPS 3 (\v0 -> v0)
= factCPS 2 (\v1 -> (\v0 -> v0) (3 * v1))
= factCPS 1 (\v2 -> (\v1 -> (\v0 -> v0) (3 * v1)) (2 * v2)) 
= factCPS 0 (\v3 -> (\v2 -> (\v1 -> (\v0 -> v0) (3 * v1)) (2 * v2)) (1 * v3))
= (\v3 -> (\v2 -> (\v1 -> (\v0 -> v0) (3 * v1)) (2 * v2)) (1 * v3)) 1
= (\v2 -> (\v1 -> (\v0 -> v0) (3 * v1)) (2 * v2)) (1 * 1)
= (\v2 -> (\v1 -> (\v0 -> v0) (3 * v1)) (2 * v2)) 1
= (\v1 -> (\v0 -> v0) (3 * v1)) (2 * 1)
= (\v1 -> (\v0 -> v0) (3 * v1)) 2
= (\v0 -> v0) (3 * 2)
= (\v0 -> v0) 6
= 6

-}


-- Direct-style implementation
fib :: Int -> Int
fib 0 = 0
fib 1 = 1
fib n = fib (n-1) + fib (n-2)

-- Accumulator-passing style won't work
-- fibAcc :: Int -> Int -> Int
-- fibAcc 0 acc = acc
-- fibAcc 1 acc = acc
-- fibAcc n acc = fibAcc (n -1) ???

-- Continuation-passing style version of `fib`
-- We've successfully written `fib` tail-recursively!
fibCPS :: Int -> (Int -> Int) -> Int
fibCPS 0 k = k 0
fibCPS 1 k = k 1
fibCPS n k = fibCPS (n - 1) (\v1 -> fibCPS (n - 2) (\v2 -> k (v1 + v2)))

{-
I don't actually recommend that you write code this way;
it's not necessary for hw2.
But it's interesting to know that it *can* be done.
-}

```


```Haskell
-- need data.set and printf for set ops and pattern matching strings like %s in C
import Text.Printf (printf)
import Data.Set 

message :: String
message = "Welcome to lecture 14!"

{-

Agenda:

- Pretty-printing
- Intro to higher-order functions
- A couple useful HOFs to know: `filter`, `map`
- hw1 comments

-}

-- The (abstract) syntax of our language
-- Syntax: What programs look like
data Expr = Number Int 
          | Sum Expr Expr
          | Diff Expr Expr
          | Prod Expr Expr
          | IfZero Expr Expr Expr
  deriving Show

-- If we wrote a parser, it would have the type signature
parse :: String -> Expr
parse = undefined

prettyPrint :: Expr -> String
prettyPrint (Number n) = printf "%d" n
-- prettyPrint (Sum e1 e2) = "(" ++ prettyPrint e1 ++ " + " ++ prettyPrint e2 ++ ")"
prettyPrint (Sum e1 e2) = printf "(%s+%s)" (prettyPrint e1) (prettyPrint e2)
prettyPrint (Diff e1 e2) = printf "(%s-%s)" (prettyPrint e1) (prettyPrint e2)
prettyPrint (Prod e1 e2) = printf "(%s*%s)" (prettyPrint e1) (prettyPrint e2)
prettyPrint (IfZero e1 e2 e3) = printf "if %s == 0 then %s else %s" (prettyPrint e1) (prettyPrint e2) (prettyPrint e3)

-- I went with the second concrete syntax for IfZero expressions:
-- if0 e1 then e2 else e3
-- if e1 == 0 then e2 else e3

-- 3 + 4
expr1 :: Expr
expr1 = Sum (Number 3) (Number 4)

-- (3 + 4) * (2 - (1 * 6))
expr3 :: Expr
expr3 = Prod expr1 (Diff (Number 2) (Prod (Number 1) (Number 6)))
-- this is an AST (abstract syntax tree)!

-- What about an AST type for lambda calculus?
data LCExpr = 
    LCLam String LCExpr -- \x -> e
  | LCVar String
  | LCApp LCExpr LCExpr -- e1 e2 

-- Let's compute the free variables of an LCExpr!
-- This is a static analysis
-- so the way this work is that it gets the set of free vars, then recursively subtracts the binded var/formal parameter in the singleton set s (singleton meaning single element set s)
freeVars :: LCExpr -> Set String
freeVars (LCVar s) = singleton s
freeVars (LCLam s e) = freeVars e `difference` singleton s
freeVars (LCApp e1 e2) = freeVars e1 `union` freeVars e2

-- (\x -> (\y -> y)) x
exampleLCExpr :: LCExpr
exampleLCExpr = LCApp (LCLam "x" (LCLam "y" (LCVar "y"))) (LCVar "x")


{-
Higher-order functions

...are functions that either
 - take functions as arguments
 - return functions as return values
 - ...or both!

HOFs let us express patterns that let us make our code a lot more concise.
-}

-- Find the first element in a list that satisfies a given predicate
find :: (a -> Bool) -> [a] -> Maybe a
find predicate [] = Nothing
find predicate (x:xs) = if predicate x then Just x else find predicate xs


-- Take a list of `Int`s and filter out the odd ones
evensOnly :: [Int] -> [Int]
evensOnly [] = []
evensOnly (x:xs) | x `mod` 2 == 0 = x : evensOnly xs
                 | otherwise      = evensOnly xs

-- Take a list of `String`s and filter out the ones that aren't of length 4
fourOnly :: [String] -> [String]
fourOnly [] = []
fourOnly (x:xs) | length x == 4 = x : fourOnly xs
                | otherwise     = fourOnly xs

{-

The only differences here are
- the type of list elements
- the guard expression

-}

-- check if f x is true and if so prepend x to next filter call, otherwise just return next filter call
filter' :: (a -> Bool) -> [a] -> [a]
filter' _ [] = []
filter' f (x:xs) | f x = x : filter' f xs
                 | otherwise = filter' f xs

evensOnly' :: [Int] -> [Int]
evensOnly' = filter' (\x -> x `mod` 2 == 0)

-- Another famous higher-order function: map

-- Take a list of `Int`s and square them
square :: [Int] -> [Int]
square [] = []
square (x:xs) = x*x : square xs

-- Take a list of `Int`s and produce a list of `Bool`s that say
-- whether each `Int` is divisible by 3.
divisibleBy3 :: [Int] -> [Bool]
divisibleBy3 [] = []
divisibleBy3 (x:xs) = (x `mod` 3 == 0) : divisibleBy3 xs

map' :: (a -> b) -> [a] -> [b]
map' _ [] = []
map' f (x:xs) = f x : map' f xs

square' :: [Int] -> [Int]
square' = map' (\x -> x * x) 

-- Take a list of `Int`s and square them
square :: [Int] -> [Int]
square [] = []
square (x:xs) = x*x : square xs

-- Take a list of `Int`s and produce a list of `Bool`s that say
-- whether each `Int` is divisible by 3.
divisibleBy3 :: [Int] -> [Bool]
divisibleBy3 [] = []
divisibleBy3 (x:xs) = (x `mod` 3 == 0) : divisibleBy3 xs

-- Take a list of predicates, apply them all to the string "rainbow",
-- and return a list of the results
applyAllPreds :: [String -> Bool] -> [Bool]
applyAllPreds []     = []
applyAllPreds (f:fs) = f "rainbow" : applyAllPreds fs

map' :: (a -> b) -> [a] -> [b]
map' _ [] = []
map' f (x:xs) = f x : map' f xs

-- Let's write all the above functions with `map` instead!
square' :: [Int] -> [Int]
square' xs = map' (\x -> x * x) xs

divisibleBy3' :: [Int] -> [Bool]
divisibleBy3' xs = map' (\x -> x `mod` 3 == 0) xs

applyAllPreds' :: [String -> Bool] -> [Bool]
applyAllPreds' fs = map' (\f -> f "rainbow") fs

-- Take a list of `Int`s and return their sum
sum' :: [Int] -> Int
sum' [] = 0
sum' (x:xs) = x + sum' xs

-- Take a list of `String`s and concatenate them
concat' :: [String] -> String
concat' [] = ""
concat' (x:xs) = x ++ concat' xs

-- Take a list of lists and return their total length
lengthAll :: [[a]] -> Int
lengthAll []     = 0
lengthAll (x:xs) = length x + lengthAll xs

{-

What's the same about `sum'`, `concat'`, and `lengthAll`?

- They all take a list, but the lists are of different types
- They all have a base case, where there's some sort of base value we want to return
- They all make a recursive call on the tail of the list
- They all combine the result of the recursive call with something,
  but they all do it in different ways

To generalize this into a pattern that will encompass all the above, we'll need:

- a polymorphic list argument, so something of type `[a]`
- something to return in the base case, which might not be the same type as the elements of the list,
  call it `b`
- a function that does the combining.  This function needs to take two arguments:
  - one will be an element of the provided list, so it has type `a`
  - one will be the result of a recursive call, so it has type `b`
  - and we should get back something of type `b`, because that's what we need to return

-}

ourFoldr :: (a -> b -> b) -> b -> [a] -> b
ourFoldr f base [] = base
ourFoldr f base (x:xs) = f x (ourFoldr f base xs)

-- Can we write this as a one-liner using ourFoldr?
-- sum' :: [Int] -> Int
-- sum' [] = 0
-- sum' (x:xs) = x + sum' xs

sum'' :: [Int] -> Int
sum'' xs = ourFoldr (+) 0 xs


-- concat' :: [String] -> String
-- concat' [] = ""
-- concat' (x:xs) = x ++ concat' xs

concat'' :: [String] -> String
concat'' xs = ourFoldr (++) "" xs

{-

- `foldr` vs. `foldl`
  - When do `foldr` and `foldl` behave differently?
- Back to our interpreter: adding variables
- If time: talk about a couple midterm questions

-}

-- Not tail-recursive!  The call `ourFoldr` is not in tail position
ourFoldr :: (a -> b -> b) -> b -> [a] -> b
ourFoldr f base [] = base
ourFoldr f base (x:xs) = f x (ourFoldr f base xs)

sum' :: [Int] -> Int
sum' [] = 0
sum' (x:xs) = x + sum' xs

sum'' :: [Int] -> Int
sum'' xs = foldr (+) 0 xs

{-
sum'' [1, 2, 3, 4]
foldr (+) 0 [1, 2, 3, 4]
1 + (foldr (+) 0 [2,3,4])
1 + (2 + (foldr (+) 0 [3,4]))
1 + (2 + (3 + (foldr (+) 0 [4])))
1 + (2 + (3 + (4 + (foldr (+) 0 [])))))
1 + (2 + (3 + (4 + 0)))
1 + (2 + (3 + 4))
1 + (2 + 7)
1 + 9
10


-}

-- sum' :: [Int] -> Int
-- sum' [] = 0
-- sum' (x:xs) = x + sum' xs

-- How would we write this tail-recursively?
sumTR :: [Int] -> Int
sumTR xs = helper 0 xs
  where helper :: Int -> [Int] -> Int
        helper acc []     = acc
        helper acc (x:xs) = helper (x + acc) xs

-- Here's foldl, a tail-recursive alternative to foldr!
ourFoldl :: (b -> a -> b) -> b -> [a] -> b
ourFoldl f acc [] = acc
ourFoldl f acc (x:xs) = ourFoldl f (f acc x) xs

sumTR' :: [Int] -> Int
sumTR' xs = ourFoldl (+) 0 xs

{-
sumTR' [1, 2, 3, 4]
ourFoldl (+) 0 [1, 2, 3, 4]
ourFoldl (+) (0 + 1) [2, 3, 4]
ourFoldl (+) ((0 + 1) + 2) [3, 4]
ourFoldl (+) (((0 + 1) + 2) + 3) [4]
ourFoldl (+) ((((0 + 1) + 2) + 3) + 4) []
((((0 + 1) + 2) + 3) + 4)
(((1 + 2) + 3) + 4)
((3 + 3) + 4)
6 + 4
10

-}

{-

Addition is associative:
(a + b) + c == a + (b + c)

So is string concatenation:
("a" ++ "b") ++ "c" == "a" ++ ("b" ++ "c")

But subtraction isn't!

-}

data Expr = Number Int 
          | Sum Expr Expr
          | Diff Expr Expr
          | Prod Expr Expr
          | IfZero Expr Expr Expr
          | Var String
  deriving Show

-- The `type` keyword lets us define a type alias
-- A list of pairs of variables and their values
-- [("x", 3), ("y", 4)]
type Env = [(String, Int)]

interp :: Env -> Expr -> Int
interp env (Number n) = n
interp env (Sum e1 e2) = interp env e1 + interp env e2
interp env (Diff e1 e2) = interp env e1 - interp env e2
interp env (Prod e1 e2) = interp env e1 * interp env e2
interp env (IfZero e1 e2 e3) = if interp env e1 == 0 then interp env e2 else interp env e3
interp env (Var s) = lookupInEnv s env

lookupInEnv :: String -> Env -> Int
lookupInEnv _ []     = error "unbound variable!"
lookupInEnv s ((name,value):xs) = if s == name then value else lookupInEnv s xs

{-

We want to interpret expressions like

(3 + x) + (5 - y)

-}

-- (3 + x) + (5 - y)
exampleExpr :: Expr
exampleExpr = Sum (Sum (Number 3) (Var "x")) (Diff (Number 5) (Var "y"))


data Expr = Number Int 
          | Sum Expr Expr
          | Diff Expr Expr
          | Prod Expr Expr
          | IfZero Expr Expr Expr
          | Var String
  deriving Show

-- The `type` keyword lets us define a type alias
-- A list of pairs of variables and their values
-- [("x", 3), ("y", 4)]
type Env = [(String, Int)]

-- data Maybe a = Nothing | Just a

{-
in the sum line, we're using case to match the value inside the parentheses, and essentially saying that we're only evaluating it if the parentheses value matches just v1, just v2 aka v1 and v2 are both ints since the fn signature has Maybe Int so they must be both integers
-}
-- in case of syntax we use arrows to evaluate the different cases
interp :: Env -> Expr -> Maybe Int
interp env (Number n) = Just n
interp env (Sum e1 e2) = case (interp env e1, interp env e2) of
    (Just v1, Just v2) -> Just (v1 + v2)
    _                  -> Nothing
-- interp env (Diff e1 e2) = interp env e1 - interp env e2
-- interp env (Prod e1 e2) = interp env e1 * interp env e2
-- interp env (IfZero e1 e2 e3) = if interp env e1 == 0 then interp env e2 else interp env e3
interp env (Var s) = lookupInEnv s env

lookupInEnv :: String -> Env -> Maybe Int
lookupInEnv _ []     = Nothing
lookupInEnv s ((name,value):xs) = if s == name then Just value else lookupInEnv s xs

-- We're not satisfied with the behavior we get from `deriving Eq` for `Expr`.
-- Let's do something better.

-- instances define the methods that a type class can use. in here we are defining the equals and not equals methods for expr.
-- now that we have defined this custom methods, in future fns we can do like e1 == e2
-- instance of the `Eq` type class for the `Expr` type
instance Eq Expr where
    (==) :: Expr -> Expr -> Bool
    (==) e1 e2 = interp [] e1 == interp [] e2

    (/=) :: Expr -> Expr -> Bool
    (/=) e1 e2 = interp [] e1 /= interp [] e2


```