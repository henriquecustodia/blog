---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular inject function: a new way to work with DI'
date: 2022-10-10T03:00:00Z
tags: []
description: ''
image: ''

---
It's time to talk about one of the best features in Angular 14: **inject function.**  I've been studying this new version for a while and I can say... Angular is getting better and better with each new version. Since the improvements in performance up to the developer's usability, it's becoming a really great framework to work with. 

> I've written a post about standalone components, it's an amazing Angular 14 feature as well. [Check out here](https://www.henriquecustodia.dev/posts/angular-standalone-components:-say-goodbye-to-ngmodules/)!

But, well, let's talk about the **inject function.** 

### What is it?

It's a new feature released in Angular 14 that allows us to inject providers (services, tokens, components, etc.) without using the constructor class. It can be handy for those moments we aren't using a class - like when creating a factory provider for instance. 

We can use this function to avoid injecting providers using the well-known class constructor. It can be good for a more cleaned approach - avoiding several injects in the constructors which can be confusing along the time. 