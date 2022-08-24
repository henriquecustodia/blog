---
title: How to inject optional dependencies in Angular like a pro
description: Usually when I create components in Angular, I need to provide a way
  to customize how Angular's logic will work.
permalink: posts/{{ title | slug }}/index.html
date: 2017-08-16T01:17:17.104Z
tags:
- angular
image: "/images/uploads/gUh1WrWMOe5UMK56GY17eA.jpeg"

---
Usually, when I create components in Angular, I need to provide a way to customize how Angular's logic will work. A good way to do this is to create a configuration class with some properties that will dictate how the logic will work. So, we could use this configuration class as follows in a component:

```js
// Config Class
export class Config {
  show: boolean = true;
}

// Component that will use the config class
@Component({ 
  selector: 'component',
  template: '<div *ngIf="show">Can you see me?</div>'
})
export class Component {  
  @Input() config: Config = new Config(); // instancia a classe para evitar erros
  show: boolean = this.config.show;
}

// Using both
<component [config]="config"><component>
```

Passing the configuration via [Input](https://angular.io/api/core/Input) is a very good way to customize the behavior of a component. However, there are cases where we need to use a certain component several times by the application, and replicating the same configuration to all the places the component is being used will be bad for the application maintenance. A good solution for this is to register the configuration class as a [Provider](https://angular.io/api/core/Provider):

```js
// config class and injection token
export const CONFIG: InjectionToken<Config> = new InjectionToken('Config')

export class Config {
  show: boolean = true;
}

// component that will use the config class  
@Component({ 
  selector: 'component',
  template: '<div *ngIf="show">Can you see me?</div>'
})
export class Component {  
  @Input() config: Config;
  show: boolean = this.config.show;
  
  constructor(@Optional() @Inject(CONFIG) private config: Config) { 
    this.config = this.config || new Config();
  }
}
```

> Note that we use the [Optional](https://angular.io/api/core/Optional) decorator to prevent Angular from not throwing an injection error if the CONFIG Provider doesn't exist.

Following the example above, we could register the custom configuration as a Provider for the entire application to consume, without needing to replicate it.

```js
// Change the default config class
export class MyCustomConfig extends Config {
  show: boolean = false;
}

@NgModule({
  // regiter the changed configuration classRegistra a configuração customizada   
  providers: [
    { provider: CONFIG, useClass: MyCustomConfig }
  ]
})
export class AppModule { }
```

Now that the CONFIG Provider has been registered in the application's root module, the custom configuration will exist wherever the component is used, so there is no need to replicate the same configuration! Simple no?!

### Here's the problem

Although the above solution is very good in terms of reuse, if the CONFIG Provider needs to be used in another component, service, or directive, we would have to replicate the treatment for when the Config Provider does not exist:

```js
constructor(@Optional() @Inject(CONFIG) private config: Config) {   
    this.config = this.config || new Config();  
}
```

### Here is the solution

The solution to further improve reuse and avoid problems with maintaining this code is to create a [Factory Provider](https://angular.io/api/core/FactoryProvider) for when the CONFIG Provider does not exist, the factory will return a default instance of the configuration. That said, let's see what the implementation looks like:

```js
// config class and injection token
export const CONFIG: InjectionToken<Config> = new InjectionToken('Config');
  
export class Config {
  show: boolean = true;
}
 
// factory function that will check whether a provider exists
export function configFactory(parent: Config): Config {
  return parent || new Config();
}

// Component that will use the config class
@Component({ 
  selector: 'component',
  template: '<div *ngIf="show">Can you see me?</div>',
  providers: [
    {
      provide: CONFIG,
      useFactory: configFactory,
      deps: [
        [new Optional(), new SkipSelf(), new Inject(Config)],
      ]
    }
  ]
})
export class Component {  
  @Input() config: Config; 
  show: boolean = this.config.showSomething;

  constructor(@Inject(CONFIG) private config: Config) { }
}
```

Note that the **deps** property receives an array with instances of decorators [Optional](https://angular.io/api/core/Optional), [SkipSelf](https://angular.io/api/core/SkipSelf), and [Inject](https://angular.io/api/core/Inject). We're telling Angular's Injector:

* **Optional**: the CONFIG Provider may not exist, so don't throw errors.
* **SkipSelf**: the Injector must look for Provider from CONFIG only in the injectors parents and not in the same Injector of the component.
* **Inject:** the Injector must inject the Provider from the CONFIG.

### Now it's good!

After all these changes, we can now inject the Provider from the Config without having to do any handling, as the config factory will be taking care of it for us!

If this article helped you in any way, be sure to share and recommend it, I guarantee there are many people with the same needs!