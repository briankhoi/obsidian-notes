
```haskell
-- varname :: type (:: used to declare type)
message :: String
-- = used to declare message
message = "Welcome to lecture 8!"

{-
-- Let's translate this to Haskell
-- TRI = \n -> ITE (ISZ n)
--                 ZERO
--                 (ADD n (TRI (DECR n)))
-- ({- msg -}) for multi line comments, -- are single line comments 
{-

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
{- another pattern matching ex:
(_:xs) pattern matches any non-empty list
- _ (underscore char) is a wildcard that matches first element of list (its head) but we don't give it a name since we don't need its value 
- : (colon) is "cons" operator which takes an element and prepends it to another list, so like "2 : (1 : []) is [2, 1]"
- xs is the rest of the list aka the entire list (its tail) excluding first element (xs is just a random var name)
so, the way the length function works is that it continuously adds one to the length count and recursively calls itself on the subset of list elements position 2 to the end until we reach an empty list
- }
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
duplicateAll (x:xs) = x : x : duplicateAll xs

-- The quiz question
-- Given:
f :: [String] -> [String]
f [] = []
f (x:xs) = ["sam"] ++ xs
-- What does the expression f ["mo", "cookie"] evaluate to?
-- Answer: ["sam", "cookie"]
```