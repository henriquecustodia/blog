---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular 15: How to use the Directive Composition API'
date: 2022-10-31T03:00:00Z
tags: []
description: ''
image: "/images/uploads/lucas-kapla-wqlagv4_oys-unsplash.jpg"

---
Angular v15 will be released pretty soon, and it's coming with a very nice feature called **Directive Composition API**.

### What is it?

The Directive Composition API allows us to compose directives into components and other directives.

This API works only with standalone components (and standalone directives). As the 14 version added the standalone property, the 15 version added a **hostDirectives** property.

Let's see how to use this property.

The example below shows a directive called **BoxDirective**, that sets some styles to the **AppComponent** using the **Composition API**:

```ts
@Directive({
  selector: 'box',
  standalone: true,
})
export class BoxDirective implements OnInit {
  renderer = inject(Renderer2);
  hostEl = inject(ElementRef).nativeElement;
  
  ngOnInit(): void {
    this.renderer.setStyle(this.hostEl, 'color', 'red');
    this.renderer.setStyle(this.hostEl, 'border', '1px solid black');
    this.renderer.setStyle(this.hostEl, 'padding', '8px');
  }
}
```

```ts
@Component({
  selector: 'app-root',
  standalone: true,
  hostDirectives: [
    RedColorDirective
  ],
  template: `
    wow
  `
})
export class AppComponent { }
```

The result will be:

![](/images/uploads/result1.PNG)

We can use this new API to reuse a lot of code. It's really amazing.

### Exposing the directive's input

When adding Inputs and Outputs to a directive, we need to expose those ones to the component to be able to use them. There are the **input** and **output** in the **hostDirectives** property because of it.

The following example adds an Input property to the BoxDirective.

```ts
@Directive({
	...
})
export class BoxDirective implements OnInit {
  ...
  @Input() color = 'red';
  ...
}
```

And we'll add the **color input** to the inputs array to expose it to the component.

```ts
@Component({
  selector: 'app-root',
  standalone: true,
  hostDirectives: [
    { 
      directive: BoxDirective,
      inputs: ['color'] // <-- exposing the directive's input
    }
  ],
  template: `
    wow
  `
})
export class AppComponent { }
```

That way we can set a color to the **BoxDirective**

```ts
<app-root color="green"></app-root>
```

The result will be:

![](/images/uploads/result2.PNG)

It's possible to rename the exposed input's name - generally useful when we have directives' inputs with the same name.

```ts
@Component({
  ...
  hostDirectives: [
    { 
      directive: BoxDirective,
      inputs: ['color: customColor']
    }
  ],
  ...
})
export class AppComponent { }
```

Using the renamed property:

```ts
<app-root customColor="green"></app-root>
```

### Exposing the directive's output

To expose outputs as easily as with inputs. But there's a property called **outputs** specifically for it.

```ts
@Directive({
	...
})
export class BoxDirective implements OnInit {
  ...
  @Input() customEvent = new EventEmitter();
  ...
}
```

```ts
@Component({
  selector: 'app-root',
  standalone: true,
  hostDirectives: [
    { 
      directive: BoxDirective,
      ...
      outputs: ['customEvent'] // exposing the directive's output
    }
  ],
  template: `
    wow
  `
})
export class AppComponent { }
```

Using it 

```ts
<app-root (customEvent)="doSomething()"></app-root>
```

In the same way as inputs, it's possible to rename the output's name.

```ts
@Component({
  ...
  hostDirectives: [
    { 
      directive: BoxDirective,
      inputs: ['customEvent: renamedEvent']
    }
  ],
  ...
})
export class AppComponent { }
```

```ts
<app-root (renamedEvent)="doSomething()"></app-root>
```

### Bonus: OnDestroyDirective

Let's create a reusable directive to destroy old subscriptions. 

The following code creates a timer component that uses an interval operator to log an incremental number every second. To avoid memory leaks, it's a good practice to remove observable subscriptions when a component has been destroyed. The **OnDestroyDirective** will remove the interval subscription automatically after the timer component is destroyed.

```ts
@Directive({
  selector: 'onDestroy',
  standalone: true,
})
export class OnDestroyDirective implements OnDestroy {
  private _destroy$ = new Subject();

  get destroy$() {
    return this._destroy$.asObservable();
  }

  ngOnDestroy(): void {
    this._destroy$.next(true);
    this._destroy$.complete();
  }
}
```

```ts
@Component({
  selector: 'app-timer',
  standalone: true,
  hostDirectives: [
    {
      directive: BoxDirective,
      inputs: ['color'],
    },
    OnDestroyDirective, 
  ],
  template: `{{ timer }}`,
})
export class TimerComponent implements OnInit {
  destroy$ = inject(OnDestroyDirective).destroy$;

  timer: number = 0;

  interval$ = interval(1000).pipe(
    takeUntil(this.destroy$),
    map((value) => value + 1),
    tap((value) => console.log(`timer: ${value}`))
  );

  ngOnInit(): void {
    this.interval$.subscribe((value) => {
      this.timer = value;
    });
  }
}
```

The result will be: 

![](/images/uploads/2022-11-01-23-32-47.gif)

### 👨‍💻

You can check out the code of this post [here](https://stackblitz.com/edit/ng-15-directive-composition-api?file=README.md).

### That's all!

I've spent some time writing this post, then, I hope that you enjoyed it!

If you liked it, give some claps/likes to this post or share it with your friends! 

Thank you for the reading. 😄

See you! 👋🏼