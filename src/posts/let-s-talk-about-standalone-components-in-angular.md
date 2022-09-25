---
permalink: posts/{{ title | slug }}/index.html
title: 'Angular Standalone Components: Say Goodbye To NgModules'
date: 2022-09-25T05:00:00-03:00
tags:
- angular
description: In this post, we will see how to use the Angular V14 feature called "standalone
  component"! That feature will make Angular applications easier and simpler to create
  and understand. Say hello to the future without NgModules!
image: "/images/uploads/ali-kokab-yv2hzor8jaa-unsplash.jpg"

---
A few months ago, Angular v14 was released with a lot of amazing new features. And, since then, I've spent some time studying and applying these to my projects.

It's hard not to admit how excited I'm feeling about the standalone component feature. This feature, in my opinion, is the most relevant improvement that Angular has had over the years. That will change how we develop our applications, and, mainly, that will make the components simpler to read and write. In the future, we probably won't need NgModules anymore.

### What's a standalone component?

Well, the standalone component is just a common Angular component that we already know but with further features. That is a combination of a component and a NgModule.

Therefore, we don't need to import our component into a NgModule to use it, because this kind of component is self-contained.

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

The code above has two new properties called `standalone` and `imports`.

But, what's it means?

* **standalone:** It marks the component as a standalone component and enables all features related to it.
* **imports:** It allows a component to import Directives, Pipes, Other Components, and existing NgModules.

### Oh, can we import a component directly into another component?

Yes, we can! We don't need to create a NgModule just to register components anymore. With standalone components, we can import a component into another component, and **Aha!** it just works.

Let's see a quick example of it.

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
@Component({
  selector: 'app-layout',
  standalone: true,
  imports: [HeaderComponent],
  template: '<app-header></app-header>',
  ...
})
```

The result will be:

![](/images/uploads/app-root-template-rendered.PNG)

In another way using NgModule we'd have to create the following code:

```ts
@Component({
  selector: 'app-header',
  template: `
    <p>header works!</p>
  `,
  ...
})
...
```

```ts
@Component({
  selector: 'app-layout',
  template: '<app-header></app-header>',
  ...
})
```

```ts
@NgModule({
  declarations: [
  	HeaderComponent, 
    LayoutComponent
  ]
})
```

It's much more verbose, isn't it?

The new approach with standalone components allows us to make Angular components simpler and more straightforward.

### And, how to bootstrap an app using only standalone components?

With this new approach, we have a new function called `bootstrapApplication`.

This function allows Angular to bootstrap an application using a standalone component directly, no more a NgModule as we've known.

```ts
bootstrapApplication(AppComponent) 
	.then(() => { })
	.catch(() => { });
```

> Generally, this code stands in `main.ts` file

With this approach, we need to change how we set the providers in the application as well. Let's see this in the next topic.

### Setting providers to the application

Yep, a lot of things have changed! 😁

The  `bootstrapApplication` function has a second parameter that allows us to add some configuration to the application.

Let's see the typescript declaration of it.

```ts
export declare interface ApplicationConfig {
    providers: Array<Provider | ImportedNgModuleProviders>;
}
```

As we can see, the `providers` property supports the `Provider` and `ImportedNgModuleProviders`.

Well, let's understand how to register a simple provider in the application - using the `Provider` type.

```ts
const ENABLE_DARK_THEME = new InjectionToken('ENABLE_DARK_THEME');

const providers: Array<Provider | ImportedNgModuleProviders> = [
	{
		provide: ENABLE_DARK_THEME,
    	useValue: true
  	}
];

bootstrapApplication(LayoutComponent, { providers })
 	.then(() => { })
	.catch(() => { });
```

And to use the registered provider is the same way as we've already known.

```ts
export class LayoutComponent {
  constructor(
    @Inject(ENABLE_DARK_THEME) enableDarkTheme: boolean
  ) { }
}
```

Nothing that new so far, just the way we register the providers have changed.

### What about routes, how can we register the app routes?

I was waiting for this question! It's changed as well. 😄

To register the app routes we have to use the new function called`importProvidersFrom`. Let's see what [Angular documentation](https://angular.io/api/core/importProvidersFrom) says about it.

> Collects providers from all NgModules and standalone components, including transitively imported.

This function will extract all providers of the NgModule and set them to the provider's app array.

Let's see how to configure the app routes:

```ts
const ROUTES: Route[] = [
	{
    	path: '',
        loadComponent: () => 
        	import('./app/pages/home-page/home-page.component')
            	.then(m => m.HomePageComponent)
    }
]

const providers: Array<Provider | ImportedNgModuleProviders> = [
  	importProvidersFrom([
    	RouterModule.forRoot(ROUTES)
  	])
];

bootstrapApplication(LayoutComponent, { providers })
  	.then(() => { })
	.catch(() => { });
```

```ts
@Component({
  selector: 'app-layout',
  standalone: true,
  imports: [
    HeaderComponent,
    RouterModule
  ],
  template: `
    <app-header></app-header>
    <router-outlet></router-outlet> // Here comes the loaded component from the router.
  `,
  ...
})
export class LayoutComponent { }
```

It's become easier to configure the app router as we can load standalone components directly using the new `loadComponent` method. We don't need to create a lazy NgModule that uses the `RouterModule.forChild` anymore.

### 💭💭

I think the Angular v14 is just the beginning of something bigger and better! Angular has changed a lot over the last few years and it's exciting.

Standalone components allow us to create simpler applications in a faster way. This new feature also can make Angular an easier framework to learn, as we won't, in the future,  need to use NgModule anymore - I hope so 😆.

> For more information about standalone components and all their features, check out the [official documentation](https://angular.io/guide/standalone-components).

***

### 👨‍💻

You can check out the code of this post on my [GitHub](https://github.com/henriquecustodia/standalone-components-example).

### That's all!

I've spent some time writing this post, then, I hope that you enjoyed it!

If you liked it, give it some claps/likes to this post; it'll help me a lot! 👏🏼❤

Thank you for the reading. 😄

See you! 👋🏼