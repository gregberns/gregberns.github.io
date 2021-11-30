---
layout: post
title: Getting Rid Of For Loops and Exiting Early
date: '2017-09-08T04:21:26.651Z'
categories: []
keywords: [software]
---

We were recently having a discussion on the value of `for` loops vs `map` and someone mentioned that map is great unless you need to exit the loop early.

For example, a loop is used (this is much simplified):

```js
var list = [1,2,3,4];  
var ret = "";  
for (var i = 0; i < list.length; i++) {  
    var item = list[i] + 1;  
    ret += item  
    if (ret.length >= 2) break;  
}  
return ret; //ret === "23"
```

The code takes an item from the list, adds one, concatenates that to the end of a string, and checks if the string is longer than 2 characters.

To me, and many functional programmers, this code is difficult to read.

Arguably, something like this is much easier to read:

```js
var list = [1,2,3,4];  
var inc = function(i) { return i + 1; };  
var pred = function(item,i,f,result) { return result.length < 2; };

mapWhile(inc, pred, list).join(""); // "23"
```

(See `mapWhile` implementation below.)

Arguably this code is:

*   Much easier to read
*   The individual functions are easier to reuse (functions: inc, pred)
*   Each component is very easy to test

We can also:

*   More easily ‘reason’ about what the individual components are doing
*   Don’t have to think about HOW to iterate over the collection

The code may need a bit more thought, but overall, there are some significant benefits over the `for` loop version.

Thoughts?

#### MapWhile Implementation

Where `mapWhile` is implemented like this:

```js
function mapWhile(fn, predicate, functor) {  
  var idx = 0;  
  var len = functor.length;  
  var result = Array(len);  
  while (idx < len) {  
    var r = fn(functor[idx]{  
    if (!predicate(r, idx, functor, result)) break;  
    result[idx] = r;  
    idx += 1;  
  }  
  result.length = idx;  
  return result;  
};
```

(This is based on the Ramda.js `map` implementation [https://github.com/ramda/ramda/blob/master/src/internal/_map.js](https://github.com/ramda/ramda/blob/master/src/internal/_map.js))

To my knowledge, `mapWhile` isn’t an FP ‘thing’/function. There is a ‘takeWhile’, but that doesn’t allow us to look at the size of the intermediate list.

Also, the predicate’s signature `function(item, index, originalArray, newArray)` is quite rough. BUT, it is in sync with the native`map` signature (thats why its structured that way).

I’m not sure this is the best implementation, but its a start. A start to cleaner, more readable code.

## EDIT

After further investigation `mapWhile` is the wrong function to be looking at. As a rule, `map` will turn all items in say an `Array` into another array with items of a different type.

Instead what were looking for is a `reduceWhile` which is available in RambdaJS. `reduce` allows us to ‘compress’ the items in a list into a single item. In this case we want to ‘compress’ the items in a list but only until a particular condition is met.

Search for ‘[reduceWhile](http://ramdajs.com/docs/#reduceWhile)’ at: [http://ramdajs.com/docs/#reduceWhile](http://ramdajs.com/docs/#reduceWhile)
