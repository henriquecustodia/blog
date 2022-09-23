---
permalink: posts/{{ title | slug }}/index.html
title: Let's talk about standalone components in Angular
date: 
tags:
- angular
- draft
description: ''
image: ''

---
A few weeks ago Angular v14 was released with a lot of new amazing features.  And, since then, I've spent some time studying and applying these to my projects.

It's hard not to admit how much excited I'm feeling about the standalone component feature. This feature, in my opinion, is the most relevant improvement that Angular had in ages. That will change how we develop our applications and, mainly, that will turn the components simpler to read and write. In the future, probably, we won't need NgModules anymore.

### What's a standalone component?

Well, the standalone component is just a common Angular component that we already know but with further features. That's a mixing of a component and NgModule. Therefore, we don't need to import our component into a NgModule to use it, because this component is self-contained.

Let's see a simple code to understand how to use it.

```ts
@Component({
  selector: 'app-header',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss'],
  changeDetection: ChangeDetectionStrategy.OnPush
})
...
```

The code above has the new properties called `standalone` and `imports`.

But, what's it means?

* **standalone:** It marks the component as a standalone component and enables all features related to it.
* **imports:** It allows a component to import Directives, Pipes, Others Component, and existing NgModules.

### Oh, can I import a component directly into another component?

Exactly! We don't need to use a NgModule just to register components anymore. With standalone components, we can import a component into another component, and **Aha!** it just works.

Let's see a quick example.

```ts
// Let's create header component
@Component({
  selector: 'app-header',
  standalone: true,
  template: `
    <p>header works!</p>
  `,
  ...
})
...
```

```ts
// Let's import the  header component into app-root component
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [HeaderComponent],
  template: '<app-header></app-header>',
  ...
})
```

The result will be: 

![](/images/uploads/app-root-template-rendered.PNG)

It works the same way for Pipes, Directives, and NgModules. 

As we can see, a standalone component has a lot of features of a NgModule. That able us to create applications with so much less boilerplate.