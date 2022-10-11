---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular inject function: a new way to work with DI'
date: 2022-10-10T03:00:00Z
tags: []
description: ''
image: ''

---
It's time to talk about one of the best features in Angular 14: **inject function.**  I've been studying this new version for a while and I can say... Angular is getting better and better. Since the improvements in performance up to the developer's usability, it's becoming a really great framework to work with.

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

It's very simple to use, indeed. I think the better part about this inject function is the possibility to create "smart functions" that can inject services doing the code easier to understand.

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

### Using factory providers

We can use inject functions inside factory providers as well. It turns easier to inject dependencies into the factory function.

```ts
 
```