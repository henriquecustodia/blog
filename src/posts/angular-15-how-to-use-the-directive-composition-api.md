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

This API works only with standalone components (and standalone directives). As the 14 version added the standalone property, the 15 version adds a **hostDirectives** property. Let's see how to use this property.

The example below shows a directive that sets the font color of the component using the **Composition API**: 

```ts
@Directive({
  selector: 'box',
  standalone: true,
})
export class BoxDirective implements OnInit {
  renderer = inject(Renderer2);
  hostEl = inject(ElementRef).nativeElement;

  ngOnInit(): void {
    this.renderer.setStyle(this.hostEl, 'color', this.fontColor);
  }
}
```

```ts
@Component({
  selector: 'app-counter',
  standalone: true,
  hostDirectives: [
    BoxDirective
  ],
  template: `
    counter
  `
})
export class CounterComponent implements OnInit { }
```