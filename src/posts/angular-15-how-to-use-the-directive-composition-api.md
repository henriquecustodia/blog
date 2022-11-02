---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular 15: Using The Directive Composition API'
date: 2022-11-02T11:00:00Z
tags: []
description: 'The directive composition API is a new feature released in Angular 15.
  It allows us to reuse the directive''s code with components and even other directives. '
image: "/images/uploads/lucas-kapla-wqlagv4_oys-unsplash.jpg"

---
Angular v15 will be released pretty soon, and it's coming with a very nice feature called **Directive Composition API**.

### But, what is it? 🤔

The Directive Composition API allows us to compose directives into components and other directives.

This API works only with standalone components (and standalone directives). As the 14 version added the standalone property, the 15 version added a **hostDirectives** property.

Let's see how to use this property.

The example below shows a directive called **BoxDirective**, that sets some styles to the **AppComponent** using the **Composition API**:

![](/images/uploads/raycast-untitled.png)

![](/images/uploads/raycast-untitled-1.png)

The result will be:

![](/images/uploads/result1.PNG)

We can use this new API to reuse a lot of code. It's amazing.

### Exposing the directive's input 

When adding Inputs and Outputs to a directive, we need to expose those to the component to be able to use them. There are the **input** and **output** in the **hostDirectives** property because of it.

The following example adds an Input property to the BoxDirective.

![](/images/uploads/raycast-untitled-2.png)

And we'll add the **color input** to the inputs array to expose it to the component.

![](/images/uploads/raycast-untitled-3.png)

That way we can set a color to the **BoxDirective**

![](/images/uploads/raycast-untitled-4.png)

The result will be:

![](/images/uploads/result2.PNG)

It's possible to rename the exposed input's name - generally useful when we have directives' inputs with the same name.

![](/images/uploads/raycast-untitled-5.png)

Using the renamed property:

![](/images/uploads/raycast-untitled-6.png)

### Exposing the directive's output 

To expose outputs as easily as with inputs. But there's a property called **outputs** specifically for it.

![](/images/uploads/raycast-untitled-7.png)

![](/images/uploads/raycast-untitled-8.png)

Using it

![](/images/uploads/raycast-untitled-9.png)

In the same way as inputs, it's possible to rename the output's name.

![](/images/uploads/raycast-untitled-10.png)

![](/images/uploads/raycast-untitled-11.png)

### Bonus: OnDestroyDirective 💎

Let's create a reusable directive to destroy old subscriptions.

The following code creates a timer component that uses an interval operator to log an incremental number every second. To avoid memory leaks, it's a good practice to remove observable subscriptions when a component has been destroyed. The **OnDestroyDirective** will remove the interval subscription automatically after the timer component is destroyed.

![](/images/uploads/raycast-untitled-12.png)

![](/images/uploads/raycast-untitled-13.png)

The result will be:

![](/images/uploads/2022-11-01-23-32-47.gif)

### 👨‍💻

You can check out the code of this post [here](https://stackblitz.com/edit/ng-15-directive-composition-api?file=README.md).

### That's all!

I've spent some time writing this post, then, I hope that you enjoyed it!

If you liked it, give some claps/likes to this post or share it with your friends!

Thank you for the reading. 😄

See you! 👋🏼