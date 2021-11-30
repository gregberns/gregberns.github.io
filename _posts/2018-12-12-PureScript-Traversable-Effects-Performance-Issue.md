---
layout: post
title: PureScript Traversable & Effects Performance Issue
date: '2018-12-12T06:42:51.752Z'
categories: []
keywords: [software, purescript]
---

When trying to process a 1 million line file, we ran into some performance issues with `traverse`.

When running 500k rows, it might take 3–4 seconds between the ‘Start’ log message and the output.

But when running 1 million lines it took 30 seconds!

```purescript
accountsText <- readTextFile UTF8 "accounts-1m.txt"  
let accounts = take 1000000 $ split (Pattern "\n") accountsText  
let accts = map (\s -> show s <> show s) accounts  
log "Start"

-- Takes 30 seconds to start processing  
_ <- traverse log accts

pure unit
```

@monoidmusician in the FP Slack suggested using `foreachE`. This immediately improved the performance issue!

```purescript
import Effect (foreachE)

accountsText <- readTextFile UTF8 "accounts-1m.txt"  
let accounts = take 1000000 $ split (Pattern "\n") accountsText  
let accts = map (\s -> show s <> show s) accounts

-- Starts processing instantly  
foreachE accts log

pure unit
```

See the [Effects documentation](https://pursuit.purescript.org/packages/purescript-effect/2.0.0/docs/Effect#v:foreachE) for more details.