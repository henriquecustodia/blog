---
permalink: posts/{{ title | slug }}/index.html
title: Creating Custom RxJS Operators
date: 2023-02-25T03:00:00Z
tags: []
description: ''
image: "/images/uploads/pawel-czerwinski-ywiowhvrbvu-unsplash.jpg"

---
Since [Angular](https://angular.io/) has become a popular framework, RxJS is becoming increasingly popular. In this post I would like to write about a very nice feature in this fantastic library: **custom operators**!

A few months ago I have been trying to keep the chaining logic more readable in my RxJS code. It's very easy to write confusing code when we don't pay attention to how the operators are being composed.

In this post, I would like to show how to write an RxJS code using custom operators to keep the logic readable and easier to maintain over time.

## How to create a custom operator?

It's very easy, really!

An RxJS operator is just a function that receives an [observable](https://rxjs.dev/guide/observable "observable") as a parameter and returns a new [observable](https://rxjs.dev/guide/observable "observable").

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

## Creating an operator to filter odd numbers

It's simple to filter odd numbers, isn't it?

But creating a custom operator to name it'll become your code cleaner and more obvious. The readability will improve.

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

## Creating an operator to search by text

Searching by text into an array might be easy, but generally doing it with RxJS turns the code a bit hard to understand over time.

But, before using a custom operator, let's see how the code would look when not using custom operators:

```ts
import { filter, from, map, Observable } from "rxjs";

const names = [
    'Henrique',
    'John',
    'Mark',
    'Bren',
    'Iarg'
];

const searchArray = ['henrique', 'b'];

from(names)
    .pipe(
        map(value => value.toLocaleLowerCase()),
        filter(value => searchArray.some((text: string) => value.includes(text)))
    )
    .subscribe(value => {
        console.log(value)
    });
```

It's not impossible to understand the code logic, but it's difficult to get what it's doing when taking a quick look.

Because of that, naming the code logic is important. 

That said, let's refactor the code above using custom operators: 

```ts
import { filter, from, map, Observable } from "rxjs";

const names = [
    'Henrique',
    'John',
    'Mark',
    'Bren',
    'Iarg'
];

const toLowerCase =
    () =>
        (source: Observable<string>) =>
            source
                .pipe(
                    map(value => value.toLocaleLowerCase())
                );


const searchByText =
    (textArray: string[]) =>
        (source: Observable<string>) =>
            source
                .pipe(
                    toLowerCase(),
                    filter(name => textArray.some((text: string) => name.includes(text)))
                );

from(names)
    .pipe(
        searchByText(['henrique', 'b'])
    )
    .subscribe(value => {
        console.log(value)
    });
    
// The result:
// henrique
// bren
```

`toLowerCase` and `searchByText` are operators that can be used to reuse the code, and it's much easier to understand what they're doing. 

## Conclusion 

Using custom operators can help us to make the code more readable and easier to reuse across the projects. 

It was a quick post about custom operators. That feature is helping me a lot to enforce the reusability in my Angular projects. 

Thank you for reading!  :)