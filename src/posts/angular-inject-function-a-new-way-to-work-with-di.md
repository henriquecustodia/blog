---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular inject function: a new way to work with DI'
date: 2022-10-10T21:00:00Z
tags: []
description: The new inject function released in Angular 14 give us a new way to work
  with dependency injection. Now, we can inject dependencies inside functions and
  a lot of more!
image: "/images/uploads/mikhail-vasilyev-nodtncsldte-unsplash.jpg"

---
It's time to talk about one of the best features in Angular 14: **inject function.**  I've been studying this new version for a while and I can say... Angular is getting better and better. Since the improvements in performance up to the developer's usability, it's becoming a great framework to work with.

> I've written a post about standalone components, it's an amazing Angular 14 feature as well. [Check out here](https://www.henriquecustodia.dev/posts/angular-standalone-components:-say-goodbye-to-ngmodules/)!

But, well, let's talk about the **inject function.**

### What is it?

It's a new feature released in Angular 14 that allows us to inject providers (services, tokens, components, etc.) without using the constructor class. It can be handy for those moments we aren't using a class - like when creating a factory provider for instance.

Also, we can use this function to avoid injecting providers using the well-known class constructor. It can be good for a more cleaned approach - avoiding several injects in the constructors which can be confusing along the time.

The following example shows how to inject a service and use it in the component:

![](/images/uploads/app-component0-ts.png)

It's very simple to use, indeed.

### Inject dependencies into functions ![👻](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABICAMAAABiM0N1AAAATlBMVEVHcEwAAAAAAAAAAAAAAAAAAAAAAAAGBgYAAAAAAAAAAAD////wOhf29vYbGxvk3Nu1tbXMzMxUVFQ6OjqVlZVkZGR2dnaFhYX0bVP3mYebPYcQAAAACnRSTlMAPHsdVQmd98TjdPFd9wAAAtJJREFUWMPtmG2b7BAMhremqjiovs3s//+jpxhVGqWz+3HzaXst9xDJk/D19WcfWtsTdjBCe9TiDzgdA4x0ze31sIz1N1E9y1r3uAMyMzQPJpSeF+lItP0ByNkwOlehe1uTZxLXk0Whe86GSHy+SeqyJG1d1f4WiT5+vjmubRRUch4mQaTioFk/NfU7I5pnzJxdX8XBZkHPHMdtrqn1kBRZEB9rvWR2tuY5fDAHhysje3BTpBwOAP8lK/dGt3HCz5ABtH8tleFtnOmmhr+ir7nSSebwL0G6LgBwEaSMt38BJIQZUQY1AZRxtqgJyZayAMocv3Cl4EpNHm/hJ/zKhK8qWTlpfV28BAkPYrS5LGjLoHjBlC8FIKmx6xlV+GXIdpZFgWJp/TPHx32u3OH/TwYHJko4fIJAE09ILZir47n4/NuNpb9klamH4pBFXhbTGTRFgqegyDRytiRnM6WgSZ3FEmXl7LCmeSIBRKZZAGLZZ+UstgCC4gnQgTjTK0HiDMIONNjiTkJUwqDBeG9LYUBQ3qB3P3WosxBoeA/Kg/Z0DBH18pxXpExsH59KnEk0PeygXdCUB6m41jI/PvYRTlvQkFPfjvMdFrSaKrKPPxw/RnRrymPQQWLVtruXSpKw3ZvoENm4LyQnJJAP39ajJO1TWwu13100SNTDUwikP+hGzHmpJeaMFzKb7UepXcAYufpCtddsV9NZ34qDIEpdWBDKltZNQcS+u+mqipiVE3xxZ9gkRG9XFyKXgRfatWxz1BTce9yYSbMeX3b6aw1ouu4fMD0VCDimx1Lf74rsXMXpcfke+6zglG40bfHg3RWrfDNyJJnbnlgZq7xhOT8xCYWRmGWhtYLaNbkmGaLXt0h3lc8RGPmOTS6zVmLrkNQwj17q71zVH1327YCge68jTUcgDEW33iDcBpO3GvO+8tFbzTYHN6jrKbEPPh1qPqH82cn+A8zAdZmKYrnHAAAAAElFTkSuQmCC)

I think the better part about this inject function is the possibility to create "smart functions" that can inject services doing the code easier to understand.

Let's see an example of a function that injects the **Renderer** service and changes the CSS class of an HTML element.

![](/images/uploads/functions-ts.png)

![](/images/uploads/app-component1-ts.png)

Have you noticed? 

The **createClassManager** and **getHost** are using the inject function. That's a good way to abstract the code into functions. Before, we had services to help us to break into pieces our code. But now, we can use functions that are a very natural way to work in Javascript.

### Where can we use this function?

Based on angular documentation:

> In practice the `inject()` calls are allowed in a constructor, a constructor parameter and a field initializer.

The inject function just works inside the injection context. We can call it when the instance is being created.

![](/images/uploads/app-component2-ts.png)

![](/images/uploads/factory-provider-ts-3.png)

### Injection Flags 🚩

Another nice thing about the inject function is the new way we can use the injection flags. Before, injecting the provider by constructor class, we had to use decorators like **@Host** or **@Optional** to change an injection behavior. Now, with inject function, we just need to set some boolean flags using an options object as the second parameter.

![](/images/uploads/inject-function-ts.png)

it's much more simple, isn't it?

Using the injection by constructor class we'd have to use the way:

![](/images/uploads/constructor-ts.png)

In my opinion, this new approach is much more simple to understand. Mainly, for those who are just starting to work with Angular.

### Should we stop to use class constructors with DI?

Well, I don't think so. 

Although the inject function is a great feature, it's just a new approach. We don't need to refactor the projects because of it. Probably class constructors and inject functions will be two options that'll coexist for a long time.

But, in my opinion, inject function brings a much more clean approach to work with DI. It's straightforward and avoids lots of parameters on the constructor. 

### 👨‍💻

You can check out the code of this post on my [GitHub](https://github.com/henriquecustodia/angular-inject-function-example).

### That's all!

I've spent some time writing this post, then, I hope that you enjoyed it!

If you liked it, give some claps/likes to this post or support me in buying a coffee; it'll help me a lot! 👏🏼❤

Thank you for the reading. 😄

See you! 👋🏼