---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular 15: How to use the Directive Composition API'
date: 2022-10-31T03:00:00Z
tags: []
description: ''
image: ''

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
export class AppComponent implements OnInit { }
```

The result will be: 

![](/images/uploads/result1.PNG)

We can use this new API to reuse a lot of code. It's really amazing.

### Exposing Inputs and Outputs from the directive

When adding Inputs and Outputs to a directive, we'll need to expose those ones to the component to be able use it.

To do it, we have the **input** and **output** in the **hostDirectives** property. 

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

```ts
@Component({
  selector: 'app-root',
  standalone: true,
  hostDirectives: [
    { 
      directive: BoxDirective,
      inputs: ['color']
    }
  ],
  template: `
    wow
  `
})
export class AppComponent implements OnInit { }
```