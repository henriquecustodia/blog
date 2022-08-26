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

I've been listening much about the micro frontend concept lately. That's a really cool way to split a big application (monolith) into small pieces that can connect with each other. It's perfect for companies that need to work with specific squads at specific parts of the application, like a cart module, for example. This approaching allow the squads to work with individual deploys and different stacks, without worrying about other projects that compose the entire solution. 

Basically, we just need to have two kind of application:

* **host**: It's an application that will load the remote applications.
* **remote:** It's an application that'll be loaded by the host application.

That's cool, right? 

Well, after that brief explanation about the micro frontend concept, let me introduce about the [Angular Module Federation](https://www.npmjs.com/package/@angular-architects/module-federation).

This module allow us to create micro frontend applications easily. 

> If you've never heard about Module Federation, [check out this post](https://medium.com/swlh/webpack-5-module-federation-a-game-changer-to-javascript-architecture-bcdd30e02669) . 

# Let's create the project

We'll use [Nx](https://nx.dev/) to create the workspace project and all applications. 

On terminal, just type the command below and generate an Angular application.

```shell
npx create-nx-workspace@latest
```

On _application name_ question, set **host **as name. That'll be the host application.

After the Nx script ran, you should have one app called **host** inside the project's workspace

![](/images/uploads/img1.PNG)

Now, let's create a new app called **remote**

```shell
nx generate @nrwl/angular:application remote --port=5000
```

If everything worked fine, you'll have a new app inside the workspace

![](/images/uploads/remote-app.png)

All good, in the next steps we're going to transform these two apps in an amazing micro frontend solution :D

# Setting the Angular Module Federation

It's time to add the Angular Module Federation in our project.

Let's type the following command on terminal

```shell
nx generate @nrwl/angular:setup-mf host --mf-type=host --remotes=remote --routing
```

Some changes has been made inside the **host** app.

* The file called **module-federation.config**  has been created

```js
module.exports = {
  name: 'host',
  remotes: ['remote'],
};
```

* The **bootstrap** file contains the main's content file.
* The **main** file load using import 