---
layout: post
title: 'Purescript: Performance with foldl to get Rights'
date: '2018-12-13T03:57:15.078Z'
categories: []
keywords: [software, purescript]
---

In Haskell, the `Either` library has a function called `rights` which takes an `Array` of `Either`s, removes all the `Left`s, and returns an `Array` of the `Left`s.

One implementation we attempted was:

```purescript
import Data.Array (snoc)
import Data.Either (Either(..))

rights :: forall a b. Array (Either a b) -> Array b
rights array =
  let
    f (Left _) accumulator = accumulator
    f (Right value) accumulator = snoc accumulator value
  in
    foldr f [] array
    
-- See this gist for the fast implementation
-- https://gist.github.com/gregberns/58c5a5701e545aa757d8a54bf61bebc2
```

The function became quite slow between 500,000 and 1,000,000 `Right` items. This is because the `snoc` function (`Array a -> a -> Array a`) is an `O(n)` function and so the larger the array becomes, the slower it becomes.

To solve this, we can use the `mapMaybe` function. This runs much more quickly without the array copying limitation.

```purescript
import Data.Either (Either(..), either, hush)
import Data.Array (mapMaybe)
import Data.Maybe (Maybe(..))

rights :: forall a b. Array (Either a b) -> Array b
rights array = mapMaybe hush array

-- Also the Lefts implementation
lefts :: forall a b. Array (Either a b) -> Array a
lefts array = mapMaybe (either Just (const Nothing)) array
```

The `mapMaybe` is an interesting function `(a -> Maybe b) -> Array a -> Array b`, which is almost like the `filter` function on arrays.

`hush` `Either a b -> Maybe b` though of limited use, its very helpful if you want to remove the `Left` case of the Either.

It also might have been possible to use `cons` (which is `O(1)`) then `reverse` to flip the list, since `cons` appends items to the front of the list. We didn’t test this implementation though.