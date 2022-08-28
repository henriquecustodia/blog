---
permalink: posts/{{ title | slug }}/index.html
title: Micro Frontend App using Angular Module Federation
date: 2022-08-26T00:00:00-03:00
tags:
- draft
- angular
description: ''
image: "/images/uploads/nathan-duck-pzruju9v-oc-unsplash-1.jpg"

---
Here I'm again after some time without publishing anything.

> This post was inspired by [Briebug's post](https://blog.briebug.com/blog/micro-frontends-angular)

I've been listening much about the micro frontend concept lately. That's a really cool way to split a big application (monolith) into small pieces that can connect with each other. It's perfect for companies that need to work with specific squads at specific parts of the application, like a cart module, for example.

This approach allows the squads to work with individual deploys and different stacks, without worrying about other projects that compose the entire solution.

Basically, we'll just need to have two kinds of applications:

* **host**: It's an application that will load the remote applications.
* **remote:** It's an application that'll be loaded by the host application.

That's cool, right?

Well, after that brief explanation about the micro frontend concept, let me introduce the [Angular Module Federation](https://www.npmjs.com/package/@angular-architects/module-federation).

This module allows us to create micro frontend applications easily.

> If you've never heard about Module Federation, [check out this post](https://medium.com/swlh/webpack-5-module-federation-a-game-changer-to-javascript-architecture-bcdd30e02669) .

# Let's create the project

We'll use [Nx](https://nx.dev/) to create the workspace project and all applications.

On the terminal, just type the command below and generate an Angular application.

```shell
npx create-nx-workspace@latest
```

On the _application name_ question, set **host** as a name. That'll be the host application.

After the Nx script ran, you should have one app called **host** inside the project's workspace

![](/images/uploads/img1.PNG)

Now, let's create a new app called **remote**

```shell
nx generate @nrwl/angular:application remote --port=5000
```

If everything worked fine, you'll have a new app inside the workspace

![](/images/uploads/remote-app.png)

All good? Perfect! In the next steps, we're going to transform these two apps into an amazing micro frontend solution :D

# Setting the Angular Module Federation

It's time to add the Angular Module Federation to our project.

Let's type the following command on terminal

```shell
nx generate @nrwl/angular:setup-mf host --mf-type=host --routing
```

The following changes have been made inside the **host** app:

* The file called **module-federation.config**  has been created

```js
module.exports = {
  name: 'host',
  remotes: ['remote'],
};
```

* The **bootstrap** file contains the main's content file.
* The **main** file loads the **bootstrap** file using an async import

The **host** app has become a shell app now. It means this app will be able to load remote apps.

Good, let's transform the **remote** app into a micro frontend app as well.

Run the following command on the terminal:

```shell
nx generate @nrwl/angular:setup-mf remote --mf-type=remote --host=host --routing
```

Did you run? Perfect! Then, just notice the **remote** app. Inside the app folder, we have now a new folder called **remote-entry,** and inside of it, there's an Angular module called **RemoteEntryModule**. This module will allow us to load the remote app inside the host app.

![](/images/uploads/remote-entry-module.png)

The **entry.module.ts** file should contain this content:

```ts
@NgModule({
  declarations: [RemoteEntryComponent],
  imports: [
    CommonModule,
    RouterModule.forChild([
      {
        path: '',
        component: RemoteEntryComponent,
      },
    ]),
  ],
  providers: [],
})
export class RemoteEntryModule {}
```

The **RemoteEntryComponent** will be the component that'll be loaded inside the **host** app.

I think that'd be good to make some changes to this component for it to look nice. Don't worry, we'll just add some style to this.

```ts
@Component({
  selector: 'app-entry',
  template: `
    <div class="container">
      <span>I'm a remote app :)</span>
    </div>
  `,
  styles: [
    `
      .container {
        border: 1px solid green;
        padding: 24px;
      }
    `
  ]
})
export class RemoteEntryComponent {}
```

Coool! Now, inside the **host** app let's add a route to load the **remote** app.

Add a file called **app-routing.module.ts** inside the app folder, with the following content:

```ts
const routes: Routes = [
    {
        path: 'remote-app',
        loadChildren: () => import('remote/Module').then(m => m.RemoteEntryModule)
    }
]

@NgModule({
    imports: [RouterModule.forRoot(routes)],
    exports: [RouterModule]
})
export class AppRoutingModule { } 	
```

> Have you noticed? The route **remote-app** will load the **remote/Module** path. It's a file that webpack will create using the configuration file **module-federation.config** inside **remote app**.

Oh! We can't forget to import the routing module into the AppModule.

```ts
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule, 
    AppRoutingModule // here
  ],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

And to finish our app, let's change de **AppComponent's template** to something nicer 

```ts
<div class="container">
    <div>I'm the host app</div>

    <a [routerLink]="['/remote-app']">load the remote app</a>
    
    <router-outlet></router-outlet> // the remote app comes here
</div>
```

Well, now it's time to run our micro frontend application 😎

Run the command below on the terminal 

```ts
nx run-many --target=serve --all 
```

Accessing the **host app** we'll see the following page:

![](/images/uploads/hpst-app-page.png)

When we click on **load the remote app** link, the **host app** will load the **remote app** using the **/remote-app** route 

![](/images/uploads/remote-app-loaded.png)

That's awesome, isn't it? With a few steps, we build a micro frontend app. 

***