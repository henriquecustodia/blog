---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular 15: How to use the Directive Composition API'
date: 2022-10-31T03:00:00Z
tags: []
description: ''
image: "/images/uploads/aron-yigin-sny6b9nspp8-unsplash-2.jpg"

---
Angular v15 will be released pretty soon, and it's coming with a very nice feature called **Directive Composition API**.

### What is it?

The Directive Composition API allows us to compose directives into components and other directives.

This API works only with standalone components (and standalone directives). As the 14 version added the standalone property, the 15 version adds a **hostDirectives** property. 

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

It's possible to rename the exposed input's name if it's needed

```ts
@Component({
  ...
  hostDirectives: [
    { 
      directive: BoxDirective,
      inputs: ['color: customColor'] // <-- exposing the directive's input
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