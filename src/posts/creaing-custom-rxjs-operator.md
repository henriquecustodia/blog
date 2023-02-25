---
permalink: posts/{{ title | slug }}/index.html
title: Creating Custom RxJS Operators
date: 2023-02-25T03:00:00Z
tags: []
description: ''
image: "/images/uploads/pawel-czerwinski-ywiowhvrbvu-unsplash.jpg"

---
Since [Angular](https://angular.io/) has become a popular framework, RxJS is becoming increasingly popAny developer needs to understando use this fantastic library and its operators. 

A few months ago I've been trying to keep the chaining logic more readable in my RxJS code. It's very easy to write confusing code when we don't pay attention to how the operators are being composed. 

That said, in this article, I want to show how to write a RxJS code using custom operators to keep the logic readable and easier to maintain over time.

## How to create a custom operator?

It's very easy, really!

A RxJS operator is just a function that receives an [observable](https://rxjs.dev/guide/observable "observable") as a parameter and returns a new [observable](https://rxjs.dev/guide/observable "observable").   

For instance:

```ts
function myCustomOperator(source: Observable<T>) { 
	return source
        .pipe(
            ...
        );
 }
 
 // using the custom operator 
 myObservable
 	.pipe(
    	myCustomOperator,
    )
    .subscribe(() => {}); 
```

Generally, it's common to create factory operators (operators that return another function), because it'll become more intuitive for the developers to understand the operators' chain.

Here's an example of a factory operator: 

```ts
function myCustomFactoryOperator() {
	return (source: Observable<T>) => { 
		return source
        	.pipe(
            	...
        	);
 	}
}

 // using the custom factory operator 
 myObservable
 	.pipe(
    	myCustomFactoryOperator()
    )
    .subscribe(() => {}); 
```

Now, let's see some examples of how to use custom operators to create more readable code!

### Creating an operator to filter odd numbers

It's simple to filter odd numbers, isn't?  

But creating a custom operator to name it'll become your code more clean and obvious. The readability will improve.

Let's see the code:

```ts
import { filter, from, interval, map, Observable } from "rxjs";

const onlyOddNumbers = () => (source: Observable<number>) =>
    source
        .pipe(
            filter(value => value % 2 === 0)
        );

interval(1000)
    .pipe(
        onlyOddNumbers(),
        take(5)
    )
    .subscribe(value => {
        console.log(value);
    });
    
// The result
// 0
// 2
// 4
// 6
// 8
```