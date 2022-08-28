---
permalink: posts/{{ title | slug }}/index.html
title: Micro Frontend App using Angular Module Federation
date: 2022-08-26T00:00:00-03:00
tags:
- angular
description: ''
image: "/images/uploads/nathan-duck-pzruju9v-oc-unsplash-1.jpg"

---
> This post was inspired by [Briebug's post](https://blog.briebug.com/blog/micro-frontends-angular)

I've been listening much about the micro frontend concept lately. A lot of companies are adopting this solution as a way to make the apps smallers and easier to deploy. 

### But, what's micro frontend? 

According to [micro-frontends](https://micro-frontends.org/ "https://micro-frontends.org/") website: 

> The idea behind Micro Frontends is to think about a website or web app as **a composition of features** which are owned by **independent teams**. Each team has a **distinct area of business** or **mission** it cares about and specialises in. A team is **cross functional** and develops its features **end-to-end**, from database to user interface.

That's a really cool! With micro frontend we can split an application into smaller pieces that focus on solve a specific problem. It's perfect for companies that need to work with dedicated squads at specific parts of the product, like a cart or checkout module, for instance.

This approach allows the squads to work with individual deploys and different stacks, without worrying about other projects that compose the entire solution.

### What do we need to create a micro frontend app?

To compose a micro frontend app, we'll just need to have two kinds of applications:

##### Host

It's an application that will load the remote applications. We can have only one host app. This app can be called as **shell** as well. 

##### Remote

It's an application that'll be loaded by the host application. We can have several these apps. Generally, remote apps are small pieces of an application. Relevant modules that needs some more attention and care. 

### Nice! And what do we're going to do?

In this post we're going to do a micro frontend application using the amazing [Angular Module Federation](https://www.npmjs.com/package/@angular-architects/module-federation) module and [Nx](https://nx.dev/) to create and manage the project's workspace.

> If you've never heard about Module Federation, [check out this post](https://medium.com/swlh/webpack-5-module-federation-a-game-changer-to-javascript-architecture-bcdd30e02669) .

### Let's create the project then

On the terminal, just type the command below and generate an Angular application.

```shell
npx create-nx-workspace@latest
```

On the _"application name"_ question, set **host** as a name. That'll be the host application.

After that, you should have one app called **host** inside the project's workspace.

![](/images/uploads/img1.PNG)

Now, let's create a new app called **remote.**

```shell
nx generate @nrwl/angular:application remote --port=5001
```

If everything worked fine, you'll have a new app inside the workspace.

![](/images/uploads/remote-app.png)

All good? 

Perfect! In the next steps, we're going to transform these two apps into an amazing micro frontend solution.

### Setting the Angular Module Federation

It's time to add the Angular Module Federation to our project.

Let's type the following command on terminal:

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

The **host** app has become a shell app now - it means this app will be able to load remote apps.

Good, let's transform the **remote** app into a micro frontend app as well.

Run the following command on the terminal:

```shell
nx generate @nrwl/angular:setup-mf remote --mf-type=remote --host=host --routing
```

Now, just notice the **remote** app. Inside the app folder, we have now a new folder called **remote-entry,** and inside of it, there's an Angular module called **RemoteEntryModule**. This module will allow us to load the remote app inside the host app.

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

### Add some style to the remote component [🐉](https://emojipedia.org/dragon/)

I think that'd be good to make some changes to this component for it to look nice. 

Don't worry! We'll just add some style to this.

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

Coool! 

### Let's add a route to load de remote app

Now, inside the **host** app, let's add a route to load the **remote** app.

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

### Almost there! 

To finish the app, let's change de **AppComponent's template** to something better

```ts
<div class="container">
    <div>I'm the host app</div>

    <a [routerLink]="['/remote-app']">load the remote app</a>
    
    <router-outlet></router-outlet> // the remote app comes here
</div>
```

```css
.container {
    border: 1px solid red;
    padding: 24px;
}
```

### Time to see the result

Well, now it's time to see the the micro frontend application running. 😎

Run the command below on the terminal: 

```ts
nx run-many --target=serve --all 
```

Accessing the **host app** we'll see the following page:

![](/images/uploads/hpst-app-page.png)

When we click on **load the remote app** link, the **host app** will load the **remote app** using the **/remote-app** route 

![](/images/uploads/ezgif-5-2222.gif)

We made a micro frontend app! That's awesome how easy it was, isn't it? 

### Curious about the code?

You can find the source code of this app at [my github](https://github.com/henriquecustodia/mfe-post).

If you're curious to see the app running on production, I've deployed this to production.  [Check out the app here](https://henriquecustodia-mf-host.netlify.app/)!  

### That's all

I've spent some time writing this post, then, I really hope that you've enjoyed! 

Don't hesitate to share this post with yours friends - I know that content can be helpful for lots of people. 

Thank you for the reading. 😄

Bye! 👋🏼