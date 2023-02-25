---
permalink: posts/{{ title | slug }}/index.html
title: Creating Custom RxJS Operators
date: 2023-02-25T03:00:00Z
tags: []
description: ''
image: "/images/uploads/pawel-czerwinski-ywiowhvrbvu-unsplash.jpg"

---
Since [Angular](https://angular.io/) has become a popular framework, RxJS is becoming increasingly popular.  It's essential for any developer to understand how to use this fantastic library and its operators. 

A few months ago I've been trying to keep the chaining logic more readable in my RxJS code. It's very easy to write confusing code when we don't pay attention to how the operators are being composed. 

That said, in this article, I want to show how to write a RxJS code using custom operators to keep the logic readable and easier to maintain over time.

## How to create a custom operator?

Actually, it's very easy. Really!

A RxJS operator is just a function that receives an [observable](https://rxjs.dev/guide/observable "observable") as a parameter and returns a new [observable](https://rxjs.dev/guide/observable "observable").   

For instance:

```ts
function myCustomOperator(source: Observable<T>) { 
	return source
        .pipe(
            ...
        );
 }
 
 // using it 
 myObservable
 	.pipe(
    	myCustomOperator
    )
    .subscribe(() => {}); 
```