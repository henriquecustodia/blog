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

Well, the standalone component is just a common Angular component that we already know but with further features. Actually, that's a mixing of a component and NgModule. Therefore, we don't need to import our component into a NgModule to use it, because this component is self-contained. 

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

#### Standalone property

It marks the component as a standalone component and enable all features related to it.

#### Imports

It's very cool! 