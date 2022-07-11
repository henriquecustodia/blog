---
title: How to inject optional dependencies in Angular like a pro
description: Usually when I create components in Angular, I need to provide a way to customize how Angular's logic will work. 
permalink: posts/{{ title | slug }}/index.html
date: '2017-08-16T01:17:17.104Z'
tags: [angular]
image: /images/uploads/gUh1WrWMOe5UMK56GY17eA.jpeg
---

Usually when I create components in Angular, I need to provide a way to customize how Angular's logic will work. A good way to do this is to create a configuration class with some properties that will dictate how the logic will work. So, we could use this configuration class as follows in a component:

Passing the configuration via [Input](https://angular.io/api/core/Input) is a very good way to customize the behavior of a component. However, there are cases where we need to use a certain component several times by the application and replicating the same configuration to all the places the component is being used will be bad for the application maintenance. A good solution for this is to register the configuration class as a [Provider](https://angular.io/api/core/Provider):

> Note that we use the [Optional](https://angular.io/api/core/Optional) decorator to prevent Angular from not throwing an injection error if the CONFIG Provider doesn't exist.

Following the example above, we could register the custom configuration as a Provider for the entire application to consume, without needing to replicate it.

Now that the CONFIG Provider has been registered in the application's root module, the custom configuration will exist wherever the component is used, so there is no need to replicate the same configuration! Simple no?!

### Here's the problem

Although the above solution is very good in terms of reuse, if the CONFIG Provider needs to be used in another component, service or directive, we would have to replicate the treatment for when the Config Provider does not exist:

```js
constructor(@Optional() @Inject(CONFIG) private config: Config) {   
    this.config = this.config || new Config();  
}
```

### Here is the solution

The solution to further improve reuse and avoid problems with maintaining this code is to create a [Factory Provider](https://angular.io/api/core/FactoryProvider) for when the CONFIG Provider does not exist, the factory will return a default instance of the configuration. That said, let's see what the implementation looks like:

Note that the **deps** property receives an array with instances of decorators [Optional](https://angular.io/api/core/Optional), [SkipSelf](https://angular.io/api/core/SkipSelf) and [Inject](https://angular.io/api/core/Inject). Basically we're telling Angular's Injector:

* **Optional**: the CONFIG Provider may not exist, so don't throw errors.
* **SkipSelf**: the Injector must look for Provider from CONFIG only in the injectors parents and not in the same Injector of the component.
* **Inject:** the Injector must inject the Provider from the CONFIG.

### Now it's good!

After all these changes, we can now inject the Provider from the Config without having to do any handling, as the config factory will be taking care of it for us!

If this article helped you in any way, be sure to share and recommend, I guarantee there are many people with the same needs!