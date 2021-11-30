---
layout: post
title: 'PureScript: Using the ST Monad for array insertion'
date: '2018-12-13T03:41:06.169Z'
categories: []
keywords: [software, purescript]
---

I wasn’t able to find any good examples of how to use the ST monad to add items to a mutable array.

The following is an implementation of Haskell’s `Either` `rights` function which takes an `Array` of `Either`s, removes all the `Left`s, and returns an `Array` of the `Left`s.

Regarding the `ST` monad, there are four pieces to the code example below:

*   The `arr <- empty` creates a new mutable array that can be mutated within the context of the `do` statement
*   `_ <- foreach array insertIfValid` is what iterates over `array` and passes`insertIfValid` each value of the array
*   `push v arr` adds the `v` value to the `arr` array
*   `freeze arr` takes the mutable array and returns an immutable array

```purescript
import Data.Array 
import Data.Array.ST (empty, push, freeze)
import Data.Either (Either(..))
import Control.Monad.ST.Internal (ST, foreach)

-- Return Rights from an Array of Either's using the ST monad
rights :: forall a b. Array (Either a b) -> Array b
rights array =
  let
    -- This seems to be required because 
    inner :: forall c. ST c (Array b)
    inner = do
      -- Create a new empty array
      -- empty :: forall a. f a
      arr <- empty
      
      let
        insertIfValid e =
          case e of
            -- push :: forall h a. a -> STArray h a -> ST h Int
            Right v -> push v arr *> pure unit
            _ -> pure unit
            
      -- foreach :: forall r a. Array a -> (a -> ST r Unit) -> ST r Unit
      -- ST.foreach xs f runs the computation returned by the       function f for each of the inputs xs.
      _ <- foreach array insertIfValid
      
      freeze arr
  in
    run inner
```
