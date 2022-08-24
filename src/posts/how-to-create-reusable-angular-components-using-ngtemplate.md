---
title: How to create reusable Angular components using NgTemplate
description: Since I started producing reusable components with Angular2+, I've always
  tried to make them as customizable as possible using Inputs and ng-content.
permalink: posts/{{ title | slug }}/index.html
date: 2017-08-16T01:17:17.104Z
tags:
- angular
image: "/images/uploads/dnVryDmlWIZwdETEVpWyug.jpeg"

---
Since I started producing reusable components with Angular2+, I've always tried to make them as customizable as possible using Inputs and ng-content. But it wasn't always possible, because for some components it's not enough to simply encapsulate content within a ready-made structure like ng-content does, there are cases where the component's content must receive data from the component itself so that it can render something more interesting.

I did some research to look for solutions that would give me more flexibility to use customizable templates in components and I found some examples on the StackOverflow of how to use ng-template to meet this need.

### Cool and how is it used?

To convey well what I'm trying to say, let's gradually create a simple component that can custom render some data.

The Component below simply renders a list of names using an array of _names._

```js
@Component({
  selector: 'my-component',
  template: `
    <ul>
      <ng-container *ngFor="name of names">
        <li>{{ name }}</li>
      </ng-container>
    <ul>
  `
})
export class MyComponent {
  names: string[] = ['John', 'Alex'];
}
```

Now let's make the component allow it to receive a customizable template so we can display the names of the _names_ array as we wish.

```js
@Component({
  selector: 'my-component',
  template: `
    <ng-template [ngTemplateOutlet]="customTemplateRef" [ngOutletContext]="{ names: names }"></ng-template>
  `
})
export class MyComponent {
  names: string[] = ['John', 'Alex'];
      
  @ContentChild(TemplateRef)
  customTemplateRef: TemplateRef
}
```

Just to explain what is happening in the component:

> _The @ContentChild Decorator will fetch a TemplateRef reference in the component's content and then inject the template reference into the ng-template that is in the component's template._

Now let's use the component with the customizable template to render the names:

```html
<my-component>
  <ng-template let-names="names">
    <ol>
      <ng-container *ngFor="name of names">
        <li>{{ name }}</li>
      </ng-container>
    </ol>
  </ng-template>
</my-component>
```

Note that we use the _let_- statement to create a template scoped reference, which allows us to render all the names the way we want.

### Hmm, but what if you don't always want a customizable template?

Let's say now that if by chance whoever is going to use the _MyComponent_ component, doesn't want to use a customizable template, but just natively show the names that are in _MyComponent._ In this case, we will need to create an internal template so that when there is no customizable template available the component uses the built-in by default.

```js

@Component({
  selector: 'my-component',
  template: `
    <ng-template [ngTemplateOutlet]="customTemplateRef || defaultTemplate" [ngOutletContext]="{ names: names }"></ng-template>
    <ng-template #defaultTemplate let-names="names">
      <ul>
        <ng-container *ngFor="name of names">
          <li>{{ name }}</li>
        </ng-container>
      <ul>
    </ng-template>
  `
})
export class MyComponent {
  names: string[] = ['John', 'Alex'];
        
  @ContentChild(TemplateRef)
  customTemplateRef: TemplateRef<any>;
}
```

That way to use the component without a customizable template would simply be like this:

```html
<my-component></my-component>
```

### Do I always need to select the context property in the template?

If you wish, you can make the object being sent in the __\[ngOutletContext\]_ directive be accessed directly without having to use the syntax:

```html
let-<ref-name>="<propName>"
```

Therefore, you will need to tell the _\[ngOutletContext\]_ directive that this context object should be accessed implicitly using the _$implicit property, like so:_

```html
<ng-template  [ngOutletContext]="{ $implicit: { names: names } }"></ng-template>
```

So to consume the data in a customizable template we would just do it like this:

```js
<my-component>
  <ng-template let-data>
    <ol>
      <ng-container *ngFor="name of data.names">
        <li>{{ name }}</li>
      </ng-container>
    </ol>
  </ng-template>
</my-component>
```

Note that _data_ is just a template scoped reference to the object we passed in _\[ngOutletContext\]:_

```html
[ngOutletContext]=”{ $implicit: { names: names } }”
```

This implicit data approach is very useful for large context objects.

### Conclusion

Using **ng-template** to work with customizable templates in your components can be a way to create less decoupled and dynamic components. And of course, more powerful.

***

If this article helped you in any way, be sure to share and recommend it, I guarantee there are many people with the same needs!