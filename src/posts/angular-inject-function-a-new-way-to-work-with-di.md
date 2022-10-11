---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular inject function: a new way to work with DI'
date: 2022-10-10T03:00:00Z
tags: []
description: ''
image: ''

---
It's time to talk about one of the best features in Angular 14: **inject function.**  I've been studying this new version for a while and I can say... Angular is getting better and better. Since the improvements in performance up to the developer's usability, it's becoming a great framework to work with.

> I've written a post about standalone components, it's an amazing Angular 14 feature as well. [Check out here](https://www.henriquecustodia.dev/posts/angular-standalone-components:-say-goodbye-to-ngmodules/)!

But, well, let's talk about the **inject function.**

### What is it?

It's a new feature released in Angular 14 that allows us to inject providers (services, tokens, components, etc.) without using the constructor class. It can be handy for those moments we aren't using a class - like when creating a factory provider for instance.

Also, we can use this function to avoid injecting providers using the well-known class constructor. It can be good for a more cleaned approach - avoiding several injects in the constructors which can be confusing along the time.

The following example shows how to inject a service and use it in the component:

```ts
@Component({
  ...
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class AppComponent {
  changeDetectorRef = inject(ChangeDetectorRef);
}
```

It's very simple to use, indeed.

### Inject dependencies into functions

I think the better part about this inject function is the possibility to create "smart functions" that can inject services doing the code easier to understand.

Let's see an example of a function that injects the Renderer service and changes the CSS class of an HTML element.

```ts
export function createClassManager(el: HTMLElement, className: string) {
    const renderer = inject(Renderer2);

    return {
        // add a class to element
        add: () => renderer.addClass(el, className),
        // remove a class from element
        remove: () => renderer.removeClass(el, className),
    };
}   

export function getHost<T>(): T {
	return inject(ElementRef<T>).nativeElement;
}
```

```ts
@Component({
  	...
  	changeDetection: ChangeDetectionStrategy.OnPush
})
export class AppComponent {
	classManager = createClassManager(getHost(), 'red-color');

  	setFontColorAsRed() {
    	this.classManager.add();
  	}

  	removeFontColor() {
    	this.classManager.remove();
  	}
}
```

Have you noticed? The **createClassManager** and **getHost** are using the inject function. That's a good way to abstract the code into functions. Before, we had services to help us to break into pieces our code. But now, we can use functions that are a very natural way to work in Javascript.

### Where can we use this function?

Based on angular documentation:

> In practice the `inject()` calls are allowed in a constructor, a constructor parameter and a field initializer.

The inject function just works inside the injection context. We can call it when the instance is being created.

```ts
export class AppComponent {
  	changeDetectorRef = inject(ChangeDetectorRef); // it works
	
	constructor() {
  		const changeDetectorRef = inject(ChangeDetectorRef); // it works
  	}
    
    ngOnInit() {
   		const changeDetectorRef = inject(ChangeDetectorRef); // it throws an error
    }
}
```

```ts
providers: [
	{
		provide: GREETINGS, 
    	useFactory: () => { 
    		const userInfo = inject(UserInfo); // it works
    		return `Hello, ${userInfo.name}!`.
  		}
    }
]	
```

### Injection Flags

Another nice thing about the inject function is the new way we can use the injection flags. Before, injecting the provider by constructor class, we had to use decorators like **@Host** or **@Optional** to change an injection behavior. Now, with inject function, we just need to set some boolean flags using an options object as the second parameter.

```ts
const service = inject(MyOptionalService, { optional: true }); 
```

it's much more simple, isn't it?

Using the injection by constructor class we'd have to use the way:

```ts
contructor(@Optional private service MyOptionalService) { }
```

In my opinion, this new approach is much more simple to understand. Mainly, for those who are just starting to work with Angular.

### Should we stop to use class constructors with DI?

Well, I don't think so. 

Although the inject function is a great feature, it's just a new approach. You don't need to refactor your projects because of it. Probably class constructors and inject functions will be two options that'll coexist for a long time.

But, in my opinion, inject function brings a much more clean approach to work with DI. It's straightforward and avoids lots of parameters on the constructor. 

### That's all!

I've spent some time writing this post, then, I hope that you enjoyed it!

If you liked it, give some claps/likes to this post or support me in buying a coffee; it'll help me a lot! 👏🏼❤

Thank you for the reading. 😄

See you! 👋🏼