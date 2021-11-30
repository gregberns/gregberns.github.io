---
layout: post
title:  "Introduction to Yoneda and Coyoneda"
date:   2021-11-28 23:00:00 -0700
categories: []
tags: [software, yoneda, coyoneda, functional-programming, haskell]
---
Yoneda (and its duel Coyoneda) is well known in the Category Theory field and has been ported over to functional languages such as Haskell. Each have their uses - sometimes in similar scenarios, also in very different ways.

This will be a newbie's explanation of the concept, using Haskell to illustrate, without any Category Theory.

From what I've read, the Yonedas are not 'daily-drivers' (not used everyday), but rather can be pulled out when the time is right. 

Uses: Both Yoneda's can be used to help speed up a program where there are many `fmap` with long lists or big trees involved. Coyoneda can be used if you want to create an 'interface' and build up calculations, then pass those calculations to an 'executor' to run them.

## Explanation of Yoneda and Coyoneda

An initial intuition for Yoneda is a partially applied `fmap`.

```hs
newtype Yoneda f a = Yoneda (forall b. (a -> b) -> f b)
```

`fmap`s signature:

```hs
fmap :: (a -> b) -> f a -> f b
```

Notice that Yodeda's type `(a -> b) -> f b` is the same as the first and last parts of Functor. This is not a coincidence. Think of Yoneda as a partially application of the second parameter `f a` of Functor.

Lets start by creating the `Yoneda` and making it a functor:

```hs
-- The following code is designed to copy-paste into a Haskell module and be run and screw around with
-- And was mostly stolen from this wonderful StackOverflow answer https://stackoverflow.com/a/24006085/684966
{-# LANGUAGE GADTs #-}
{-# LANGUAGE RankNTypes #-}

newtype Yoneda f a = Yoneda {runYoneda :: forall b. (a -> b) -> f b}

instance Functor (Yoneda f) where
  fmap f y = Yoneda (\ab -> runYoneda y (ab . f))
```

Take some time looking at the implementation of `fmap`. Its should feel like `fmap` from `Option`/`Maybe`. There were two things I noticed that seem key:

* Its returning a new Yoneda, wrapping a function. This provides delayed execution.
* See `ab . f`, which is composing or building up the functions to be run. This will allow `fusing` the functions provided to the `fmap`. This means composing the functions and executing them against, for example, a large `List`, in a single pass.

Now lets create two functions, one to 'lift' (`toYoneda`) a functor into Yoneda, and a second to 'lower' (`fromYoneda`) a value out of the Yoneda.

```hs
toYoneda :: Functor f => f a -> Yoneda f a
toYoneda fa = Yoneda (\f -> fmap f fa)

fromYoneda :: Yoneda f a -> f a
fromYoneda y = runYoneda y id
```

Lets use it. First, lets just implement `Option` so we can see the guts of it.

```hs
data Option a
  = Some a
  | None

-- Comment this out - Notice when calling toYoneda, it fails to compile because Option isnt a Functor
instance Functor Option where
  fmap f (Some a) = Some $ f a
  fmap _ None = None
```

Now lets implement something trivial with the `Option` and `Yoneda`. 

Below, (first expression) `createYo` is simply `Some 1` 'lifted' into a `Yoneda`. Then (second expression) we `fmap` twice, which will modify the `1` in the functor and we are passing `createYo` into that `fmap` chain. Then we pass to `fromYoneda` which will evaluate the expression.

```hs
createYo :: Yoneda Option Integer
createYo = toYoneda $ Some 1

res :: Option Integer
res = fromYoneda $ fmap (+ 1) $ fmap (+ 3) createYo
```

Why is this useful? Well, lets say our functor was a very large list, and there were numerous `fmap`s, then the functions are 'fused' together - or composed together and run once on each item in the list.

## Coyoneda

Lets look at Coyoneda. There are a couple things to check out:

* The type signature has 'backward arrows' (thats where the `Co` comes from - all the arrows switch directions). 
* Compared to `Yoneda`, there is no lambda function.
* But the functions `f . ba` are still composed together.

```hs
data CoYoneda f a = forall b. CoYoneda (b -> a) (f b)

instance Functor (CoYoneda f) where
  fmap f (CoYoneda ba fb) = CoYoneda (f . ba) fb
```

We'll also create similar 'lift' and 'lower' functions:

```
toCoYoneda :: f a -> CoYoneda f a
toCoYoneda fa = CoYoneda id fa

fromCoYoneda :: Functor f => CoYoneda f a -> f a
fromCoYoneda (CoYoneda mp fb) = fmap mp fb
```

And lets use it. (We'll also create another `Option` like thing - you'll see below why.)

It looks very similar - like the same.

```hs
data Dirty a
  = Filthy a
  | Clean

instance Functor Dirty where
  fmap f (Filthy a) = Filthy $ f a
  fmap _ Clean = Clean

-- createCoyo :: CoYoneda Dirty Integer
createCoyo = toCoYoneda $ Filthy 1

-- resCo1 :: Dirty Integer
resCo1 = fromCoYoneda $ fmap (+ 1) $ fmap (+ 3) createCoyo
```

Now you ask: "Why is it valuable then?".

Lets add two more functions. The first is a Natural Transformation of a `Coyoneda` - that means we are going to change the structure of the data, but not lose any data. "What the hell is the purpose of that?" you ask? This will allow us to swap out the `Dirty` data structure for another one - `Option` we used above. `swapDirtyForOptional` is just a helper function to swap between the data structures.

```
ntCoyo :: (forall a. f a -> g a) -> CoYoneda f a -> CoYoneda g a
ntCoyo f (CoYoneda g fa) = CoYoneda g (f fa)

swapDirtyForOptional (Filthy a) = Some a
swapDirtyForOptional Clean = None
```

Finally, lets evaluate the thing, but we'll break it down into steps to see clearly whats going on.

Again, we're just `fmap`ing over the `CoYoneda`. Then `ntCoyo` changes the value from `CoYoneda Dirty Integer` to `CoYoneda Option Integer` - or swaps `Dirty` for `Option` - again, without losing any data. Finally we 'lower' the value out of the Coyoneda.

```hs
-- Switch out Dirty, which was provided initially, for Option
resCo21 :: CoYoneda Dirty Integer
resCo21 = fmap (+ 1) $ fmap (+ 3) createCoyo

resCo22 :: CoYoneda Option Integer
resCo22 = ntCoyo swapDirtyForOptional resCo21

resCo23 :: Option Integer
resCo23 = fromCoYoneda resCo22
```

Whoa! We were able to transform the Functor `Dirty` while it was in the `CoYoneda` structure. Remember the `Filthy 1` we supplied has not been modified yet - because the function to modify the value has not been run against it yet - so we can just swap it out with `Option`.

Here's the really cool part of Coyoneda compared to Yoneda.

Comment out the `Functor Dirty` instance.

```hs
instance Functor Dirty where
  fmap f (Filthy a) = Filthy $ f a
  fmap _ Clean = Clean
```

First notice how `createCoyo` works still. This means we can construct a Coyoneda without a functor instance being provided - so it can pretty much be anything we want!

Second, see that `resCo1` throws an error. This is because for the `CoYoneda` to be lowered, we need a Functor instance to be supplied, so `fromCoYoneda` can execute the `fmap`. 

Last notice that `resCo23` is not erroring. Thats because we swapped `Dirty` for `Option` and `Option` has an implementation of `Functor` so `fmap`.

Takeaway: One type could be lifted into a Coyoneda and a computation produced. Then before its lowered, a different implementation could be swapped in.

## Conclusion

* Yoneda can provide 'fusing' if lists or trees are large
* Coyoneda can 'fuse' a computation on one type and apply it on another

There are plenty more uses and more to learn - but hopefully thats a quick intro that provides some intuition.

## Resources

* Full code implementation - [https://stackoverflow.com/questions/24000465/step-by-step-deep-explain-the-power-of-coyoneda-preferably-in-scala-throu#24006085](https://stackoverflow.com/questions/24000465/step-by-step-deep-explain-the-power-of-coyoneda-preferably-in-scala-throu#24006085)
* Good intro video to the type signatures, with lift and lower - [https://vimeo.com/122708005](https://vimeo.com/122708005)
* A Category Theory intro - [https://bartoszmilewski.com/2015/09/01/the-yoneda-lemma/](https://bartoszmilewski.com/2015/09/01/the-yoneda-lemma/)
* Joke Tweet: [https://twitter.com/runarorama/status/1324783319670792192?s=21](https://twitter.com/runarorama/status/1324783319670792192?s=21)
* [https://blog.ocharles.org.uk/posts/2017-08-23-extensible-effects-and-transformers.html](https://blog.ocharles.org.uk/posts/2017-08-23-extensible-effects-and-transformers.html)
* [https://slides.yowconference.com/yowlambdajam2017/Humphries-ContinuationsAllTheWayDown.pdf?feature=oembed](https://slides.yowconference.com/yowlambdajam2017/Humphries-ContinuationsAllTheWayDown.pdf?feature=oembed)
* [https://comonad.com/reader/2011/free-monads-for-less-2/](https://comonad.com/reader/2011/free-monads-for-less-2/)
* Small construction in JS: [https://gist.github.com/DrBoolean/c0204ed57cf63a70dfe0](https://gist.github.com/DrBoolean/c0204ed57cf63a70dfe0)
* Free [https://blog.higher-order.com/blog/2013/08/20/free-monads-and-free-monoids/](https://blog.higher-order.com/blog/2013/08/20/free-monads-and-free-monoids/)
* Yoneda [https://blog.higher-order.com/blog/2013/11/01/free-and-yoneda/](https://blog.higher-order.com/blog/2013/11/01/free-and-yoneda/)
* Yoneda in Rose tree [https://gist.github.com/i-am-tom/889e02021844acf8ec764236913b7956](https://gist.github.com/i-am-tom/889e02021844acf8ec764236913b7956)

