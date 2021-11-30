---
layout: post
title: When to use Map vs forEach
date: '2017-09-08T08:46:44.304Z'
categories: []
keywords: [software]
---

(Note: to fully explain this topic, I need to take a bit of a detour. Hang in there, we’ll get there quickly)

There is a tendency in OOP (Object Oriented Programming) code to mutate, or change, an object by its reference. But sometimes the caller may not know the change is occurring.

Lets look at an example:

```js
var list = [1,2,3,4];  
var a = "";  
for (var i in list) {  
    changeMe(i);  
}  
function changeMe(item) {  
    item = item + 1;  
    a += item;  
}
```

Does the caller (the `for` loop) know that `a` is being modified? Probably not.

This ‘mutation’/change problem can wreak havoc when it comes to reusing methods. You have a method that looks like it will do what you want, but when you use it, it causes bugs elsewhere. I’m sure you’ve never seen this before…

Imagine if there were several items supplied to a method, and the method signature looked like this:

```js
function (array, object)
```

Without looking at the body of the method, do you have any idea what the method does? Is the object acting on the array? Is the array acting on the object? Are they both changing? Is something you’re not aware of changing?

The problem is that we don’t know.

What about a slightly modified function like this:

```js
function (array, object) : array // the array is returned
```

In this case, we still may not know, but if this pattern is used consistently, we might be able to assume that the array is being modified because it is being returned. Maybe even the object is being added to the array…(hint, hint)

What if we made a RULE that any parameters could not be modified, and instead if you wanted to change something you had to return it?

What does that change about our function? What can we now assume?

```js
function (array, object) : array // array is returned
```

Well, we can’t add the array to the object, because the object isn’t returned. But maybe it is safe to assume that the object was added to the array because the array was returned. This gives us a little more confidence when trying to reuse this method.

The bugs come when we start using methods that we have no ‘guarantees’ about. This will generally be fine:

```js
function (array, object) {   
    array.push(object);   
    return array;  
}
```

But what if the method creator did this instead:

```js
function (array, object) {   
    array.push(object);  
    a = array.length;  
    return array;  
}
```

Where did the `a` come from? What else does it impact? We have no good way of knowing. This is what is meant when we say methods ‘change the world’. They can change things outside of our direct control.

The objective of this post is to suggest that moving away from using ‘methods’ that can ‘change the world’ (meaning we have no idea what they will impact), and move toward using ‘functions’ that changes only things in their direct control, we can get an incredible amount of safety when writing our code.

#### Most of the Time We Don’t Want to Change the World

Though ‘we all want to change the world’, we usually want to do it on purpose. It may seem cool at first if every action we took had the possibility of changing things around us, but lets think about it further.

When you’re driving, and you turn the steering wheel, you don’t also want to accelerate. When you turn on the toaster, you don’t also want to run the dishwasher. So lets not make our programs do the same.

If we want to add a value to an array, lets supply an array and an item and get back the array with the item added to it. (In ‘functional programming land’ we usually will get a new array back, but lets not worry about that for now.)

Based on that, if we see a function that looks like this:

```js
function (array, object) : array // array is returned
```

We can start making assumptions:

*   the object was simply added to the array, and the array was returned
*   the object was removed from the array, and the array was returned

```js
var list = [1,2,3]  
list = append(list, 4); // list is now [1,2,3,4]  
function append(array, object) : array // array is returned
```

Another example, lets update an object:

```js
var obj = { x: 1, y: 2 }  
obj = updateX(obj)  
function updateX(obj){  
    obj.x = obj.x + 1;  
    return obj;  
}
```

The object was changed, but NOTHING else was modified.

This may seem trivial at first, and in some cases it might be, but when you start building and using Functions and not Methods, you’ll see a shift. Your code will start to become more reusable and more readable and understandable.

When our code is more understandable, we say it is ‘easier to reason about’. Another way to put it: it is easier to understand the implications of what will happen when a Function is called.

#### Lets Apply this to Map

If we use the example from above, and we apply it to a list of items:

```js
var list = [{ x: 1, y: 2 }, { x: 2, y: 3}]
list.map(i => {
    obj.x = obj.x + 1;
    return obj;
})
```

We can SAFELY refactor the inner function out:

```js
var list = [{ x: 1, y: 2 }, { x: 2, y: 3}]  
function updateX(obj){  
    obj.x = obj.x + 1;  
    return obj;  
}  
list.map(i => updateX(i))
```

We could even move this to a new module with very little impact. Also, notice `updateX` is now quite easy to reason about and also very easy to test.

OOP has always pushed the concept of ‘DRY’ (Don’t Repeat Yourself). Using this refactoring process will allow you to create many small methods that are very reusable and testable, so you can keep your code DRY.

A more strongly typed language, can also provide benefits because you can restrict `updateX` to only take an object that contains `x` as a property (with an interface):

```js
function updateX(IHasX : obj)
```

The `map` function should ALWAYS take these kinds of functions. It should NEVER take functions that mutate or change things outside of its control.

BAD:

```js
var list = [{ x: 1, y: 2 }, { x: 2, y: 3}]  
var index = 0;  
function updateX(obj){  
    index += 1  
    obj.x = obj.x + index;  
    return obj;  
}  
list.map(i => updateX(i))  
function changeIndex() {  
    index *= 2  
}
```

The problem is that `updateX` now doesn’t run consistently if `changeIndex` is called. One time it might run and `index` is 4 and another time `index` is 8. This concept of returning the same thing every time is called ‘functional purity’. ‘Function purity’ or ‘pure functions’ are important, but a topic for another time.

**Remember this**: `map` is used to convert every item in a list from `a -> b` or changing a list from type `a` to a list of type `b`, but nothing else.

When you use these functions that return the same thing every time, pure functions, you get much more readable and understandable code (though it might take a short bit to get used to using it).

To extend the idea a bit further, we can also create a second function used by `filter` to filter the list down.

```js
var list = [{ x: 1, y: 2 }, { x: 2, y: 3}]  
function updateX(obj){  
    obj.x = obj.x + index;  
    return obj;  
}  
var filterEvens = function(i) { return i % 2; };  
list.map(i => updateX(i))  
    .filter(i => filterEvens(i)); // returns [{ x: 2, y: 2 }]
```

Again, `filterEvens` is a simple, reusable function thats easy to check for validity.

#### Sometime You Want To Change The World

There are other times you want to change your surroundings. For example, once your done processing a list, you want to write it to the `console`. In this case, we don’t want to use `map`.

Again, remember, `map` us used to ‘transform’ `a -> b`, and **not** effect the world. And this is where `forEach` comes in. Let’s use our example from before:

```js
list.map(i => updateX(i))  
    .filter(i => filterEvens(i)); // returns [{ x: 2, y: 2 }]
```

We now want to output our result to the `console`. We don’t want to use `map` because in this case we want to go from `a -> void` or from something (type `a`) to nothing, and we need to have an effect on the ‘world’ (in this case the console).

So lets use `forEach` at the end of our expression:

```js
list.map(i => updateX(i))  
    .filter(i => filterEvens(i))  
    .forEach(i => console.log(i); //console: [{ x: 2, y: 2 }]
```

We now have done a transform, filter, and Effected The World!

Before, we were just holding the results of the transform and filter close to our chest (in memory), now we’ve let the ‘world’ know that something has occurred.

This idea is quite important. As an overly simplistic rule, if you don’t expect anything to be returned from a function, it is probably going to effect something in ‘the world’ such as: database insert, http delete, console.log(), etc.

#### Conclusion

Though these ideas may seem simplistic, they are very powerful. The simplest of concepts can be the most powerful.

At first, adding a new separate function may seem more work than its worth. Using a `map` may seem foreign and uncomfortable. But with a little work it will seem natural. By using these concepts I believe you will see four positive outcomes:

*   You will have fewer bugs
*   Your code will become much easier to read and understand
*   Individual functions will become much easier to reuse
*   Each function will be very easy to test

"FEWER BUGS!!" You say?!?!

Yes, its true. But until you try it, you’ll never believe it. Try it, you’ll see.

Let me know when you see.