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