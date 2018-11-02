---
layout: post
title: "Fast Exponentiation"
date: 2017-06-20
categories: haskell
---

## Exponentiation

G'day ladies and gents, as a quick test run of my *magical* new Jekyll powered blog, I'm going to run through the magic of super fast exponentiation.

Naively, if I were to go ahead and write an exponentiation function to calculate a^b then I would go ahead and write something like this:

```c
int exp(int a, int b) {
    int r = 1;
    while (b > 0) {
        r *= a;
        b--;
    }
    return r;
}
```

and equivalently 

```haskell
exponential :: Int -> Int -> Int
exponential _ 0 = 1
exponential a b = a * (exponential a (b-1))
```

Now, this is all well and good, but we can (and should) go faster. Enter stage: Fast Exponentiation. 

## Fast Exponentiation

The algorithm is as follows

```c
int faster_exp(int a, int b) {
    int r = 1;
    while (b > 0) {
        if (b % 2 == 1) {
            r = r * a;
        }
        a = a * a;
        b = b / 2;
    }
}
```

and in haskell

```haskell
faster_exp :: Int -> Int -> Int
faster_exp _ 0 = 1
faser_exp a b | even b    = a * (faser_exp (a * a) (quot b 2))
              | otherwise = (faser_exp (a * a) (quot b 2))
```

## Let's generalize this

For the sake of argument, say we get yelled at by someone to calculate some fibonacci numbers as quick as we can. Being savvy people of the internet generation, we can see that a really snazzy way to do this is to chuck it in a two by two matrix, raise it to the i'th fibonacci number we want, and then snag either the bottom left or top right element from the matrix. Let's have a look at how we would do this in Haskell.

```haskell
-- Create our 2x2 matrix 
data Matrix a = Matrix a a a a
  deriving (Show)

-- Let's define a really simple way of multiplying these together
mult :: Num a => Matrix a -> Matrix a -> Matrix a
mult (Matrix a b c d) (Matrix x y z w) = Matrix (a*x + b*z) (a*y+b*w) (c*x+d*z) (c*y+d*w)

-- Rewriting our exponentiation function we have
simple_exp :: Num a => Matrix a -> Integer -> Matrix a
simple_exp _ 0 = Matrix 1 0 0 1
simple_exp a b = a `mult` (simple_exp a (b - 1))

-- Then
fib_initial = Matrix 1 1 1 0
get_fib (Matrix _ _ n _) = n

nth_fib n = get_fib $ simple_exp fib_initial n
```
Obviously if I were to do this to any proficient degree I would be defining a proper Num instance for our bare bones matrix, and possibly use some type indices to encode some type-safe, but adjustable dimensioned, matrices.

At this point I've had enough of playing around with blogging, so have fun and see y'all!
