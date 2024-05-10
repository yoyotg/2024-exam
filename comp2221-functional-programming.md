# Appendix for COMP2221 Exam Notes - Functional Programming
### Side Effects
```f(1)``` changes everytime since ```var``` is modified everytime - But this is not known just by looking at the function
(Written in Python : )
```py
var = 0
def f(n):
    var += 1
    return n + 1
```

### Basic Composition of a Function
General Syntax :
```haskell
-- This is the type signature
function-name :: input -> output
function-name arg = instruction
-- Only the last one will be the output
n-ary-function :: input -> input -> input -> output
```
Example :
```haskell
-- add takes an int, then another int, and outputs an int
add :: Int -> Int -> Int
add x y = x + y
```
Function composition :
```haskell
odd a = not (even a)
-- Equals to 
odd a = not . even a
```
\
### Types
##### List of Types
| Types | Meaning | 
| ----- | ------- |
| ```a``` | Anything |
| ```Int``` | Integer |
| ```Float``` | Float |
| ```Double``` | Double |
| ```Bool``` | Boolean |
| ```Char``` | A single quote character |
| ```[a]``` | List - a list of anything in this case |
| ```(a, b)``` | A tuple |
| ```(a -> b)``` | Functions |
| ```(a -> a -> b)``` | n-ary functions |

Applying types to functions :
```haskell
not :: Bool -> Bool
sum :: Num a => [a] -> a
-- Type class (Num here) explained below
```
\
##### Defining and Using Operators
General Syntax : 
```haskell
-- If it is a symbol : Use () in front
(*) :: input -> input -> output
arg1 * arg2 = * arg1 arg2
-- If it is a word : Use `` in middle
operator arg1 arg2 = arg1 `operator` arg2

```
Examples : 
```haskell
1 + 2 = (+) 1 2
mod 3 2 = 3 `mod` 2
```
\
##### Adding New Types
```type``` : Create new types from existing types
```haskell
type IntList = [Int]
function :: IntList -> Bool
-- Is interchangable with
function :: [Int] -> Bool
```
```data``` : Create a completely new type by specifying allowed values
```haskell
data IsTrue = Yes | No | Perhaps
-- Values can have types
data Shape = Circle Int | Rect Int Int
-- Not all values need types
data Maybe a = Nothing | Just a
-- Calling them in functions : Make a implementation for each value
check :: Maybe -> Maybe
check Nothing = Nothing -- Nothing originally doesn't have a type (i.e. a), so no type here
check Just x = Just x
-- Sum type (OR) using |
data Foo = A | B
-- Product type (AND) just putting them together
data Shape = Rect Int Int
```
\
### Classes and Instances
##### Creating a Class and Instances
```haskell
-- A class called IsInt
class IsInt a where
    -- This function should apply to every value in the class IsInt
    fuctionIsInt :: a -> Bool
-- Create an instance where the value of IsInt is an integer
instance IsInt Int where
    functionIsInt _ = True
instance IsInt Char where
    functionIsInt _ = False
```
List of (default) type classes :
| Type classes | Meaning |
| ------------ | ------- |
| ```Eq``` | Comparable using ```==``` |
| ```Ord``` | Orderable using ```<```, ```>```, ```<=```, ```>=```, ```min``` and ```max```. Include ```Char```, ```Int```, and ```Bool``` |
| ```Num``` | Numbers|
| ```Functors``` | Mappable |
| ```Foldables``` | Reducible |
| ```Monad``` | Provide structure and composition |

\
### Lists
##### Creating Lists
Construct using ```:```  = A value and a list:
```haskell
1 : [2, 3, 4] = 1 : (2 : (3 : (4 : [])))
```
Construct using ```++``` = A list and a list :
```haskell
as ++ [x] = [1, 2, 3, 4] ++ [x]
```
\
##### Pattern Matching
Matching list with unspecified length :
```haskell
unspecified ('a':_)
-- Matches ['a', 'b'], ['a', 'b', 'c'], etc.
```
Matching list with fixed length :
```haskell
fixed ['a',_,_]
-- Matches ['a', 'b', 'c'] but not ['a', 'b']
```
Value binding :
```haskell
returnFrist (a:_) = a
notAllowed (a:a:_) = a -- Variables can't have the same name
allowed (_:a:_) = s -- The first _ takes only one character
rest (a:as) = as -- as is a list
```
\
##### List Comprehension
General syntax :
```haskell
[value | generator(s), guard(s)]
```
Example - Odd numbers within [1..5] :
```haskell
[x | x <- [1..5], mod x 2 == 0]
-- [1..5] has two dots .. only
```
Guards must come later than generators that are used :
```haskell
[(x, y) | x <- [1..5], even x, y <- [x..7], even y]
-- (x, y) will generate all combinations of x and y
-- Later generators can reference earlier generators 
-- i.e. y <- [x..7] in this case
```
### Examples

##### [2019PP-Q2.c]
```haskell
sumChoose selector x y = sum . map (uncurry selector) $ zip x y
```
Explanation :
```$ zip x y``` = A replacement for brackets (i.e. The following part is in a bracket) = ```(zip x y)```

```zip x y``` = Create pair for the ith element of both ```x``` and ```y```, e.g. :
```haskell
x = [1, 2, 3]
y = [4, 5, 6]
zip x y -- (1, 4) (2, 5) (3, 6)
```
```uncurry``` = Take the following function (that should take two arguments) and feed a pair to it as two argument, e.g. :
```haskell
uncurry selector (x1, y1) = selector x1 y1
```

```sum . map``` = Sum the following list that got mapped

Type signature :
``` haskell
sumChoose :: Num a => (a -> a -> a) -> [a] -> [a] -> a
```
\
##### [2020PP-Q1.f.ii]
Writing ```map``` using ```foldr``` :
```haskell
map f xs = foldr (\x xs -> (f x) : xs) [] xs
```
* No need to add ```(x:xs)``` in ```map``` yet cus this will be decoupled when passed to ```foldr```
* ```(\x xs -> (f x) : xs)``` is the actuall mapping part
