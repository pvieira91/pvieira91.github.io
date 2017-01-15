---
layout: post
title:  "Run promises sequentially"
date:   2016-10-02 23:43:00
categories: Promises, Javascript
---



## Promises mixed with ES2015 higher-order functions

Work in progress

Simply put, promises are great. Really great. They are quite simple, semantic and,  most important, they have completely changed the way we deal with async code (thank god!).
Mixed with the ES2015 higher-order functions (like reduce or map) we can achieve pretty outstanding results with few lines of code.

#### Examples

Let's imagine we have an array representing a list of messages that we want to delete. In order to delete a message we need to make a XHR request which is async.
This situation is well-suited for the use of `map` (each message will be transformed into a promise). 

```javascript
Promise
  .all(messages.map((message) => MessagesApi.delete(message.id))) //map creates an array of promises
  .then(() => {
    console.log('All messages have been deleted :)');
  })
  .catch(() => {
    console.log('oops... something went wrong');
  })
```

Another great example, this time using reduce function, is execute an array of promises sequentially. 
Let's suppose that, for some reason I can't imagine, those messages must be deleted sequentially. A message can only be deleted if the previous has been successfully deleted.

```javascript
messages
  .map((message) => () => MessagesApi.delete(message.id))
  .reduce( (p, fn) => p.then(fn), Promise.resolve()) //arrayOfPromises[0].then(arrayOfPromises[1]).then(arrayOfPromises[2])....then(arrayOfPromises[n-1])
  .then(() => {
    console.log('All messages have been sequentially deleted :)');
  })
  .catch(() => {
    console.log('oops... something went wrong');
  })
  
  //Note: We could elaborate a bit more on this and implement a mechanism to know which promises have failed and which were successfull but that's the idea of this post.
```

