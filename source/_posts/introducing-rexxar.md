title: Introducing Rexxar
date: 2017-06-05 22:00:09
tags:
  - work
  - front end
  - summary
  - rexxar
  - hybrid
  - mobile
categories: work
---

Rexxar is a mobile development solution for cross-platform application. We design Rexxar to be non-intrusive and lightweight. It is now open source with three libraries.
* [Rexxar iOS Container](https://github.com/douban/rexxar-ios)
* [Rexxar Android Container](https://github.com/douban/rexxar-android)
* [Rexxar Web SDK](https://github.com/douban/rexxar-web)

The word Rexxar is originally from Warcraft. Rexxar is one of the warriors who had been formed from a union between orcs and ogres.

<img src="/img/introducing-rexxar/logo_cartoon.png" alt="Logo cartoon" style="border:0;width:382px">

<!-- more -->

## Background

Before Rexxar, we had several hybrid pages in Douban App, which were extremely simple and primitive. One common way to do this, is to open online web sites, which has disadvantages like slow speed and bad user experience. Another way, that is at the opposite extreme, we put the offline HTMLs into the app, and make ajax calls for the data, which speed up the initial loading but still lose the flexibility of web technology.

## Motivation

We mainly work on Douban App. With the expansion of product line, engineering team spent more and more time supporting both iOS, Android and Web. Meanwhile, we need to keep iterating fast. Delivering new feature every two weeks pushed us to think about efficiencies and costs.

Different features and pages are distributed in our App. Many of them user-generated articles and reviews. These are rich text pages which are more of content displaying than complex animation. It is appropriate to use web technologies.

When prioritize the benefits we want to gain from hybrid mobile app development, we found the top three was:
* **Fast development**
* **Write once run everywhere**
* **Hot code patch**

## Core Principles

Studying existing frameworks is a good starting point. However, we found neither of them perfectly meets our needs. At the same time, we’d better have full control of the framework so that we could easily integrate hybrid pages into native pages without affecting existing development process or app bundle size.

Our Rexxar is not exactly a framework. It was designed to be **non-intrusive**, which means, developers can write Rexxar pages without even knowing the existing of the libraries. It’s just HTML, CSS and JavaScript code you write that could be run anywhere with a web browser and loosely coupled to native.

Rexxar is also **lightweight**. Instead of compiling JavaScript to native code or building our own rendering engine, we decided to use WebView to render our components. Through this way, we could concentrate on product development and make the most use of advanced web technology.

Although based on WebView, Rexxar is much **powerful**. we equip it with much more capabilities such as cache for static assets, native-like widgets, persistent storage, etc.

Moreover, Rexxar is **scalable**. We design two levels of granularity that are full-rexxar view and partial-rexxar view. The full-rexxar means that the full page is implemented and controlled by Rexxar, while the partial-rexxar view refers to a page which could not run independently and should be combined with some native part.

## Technical Overview

Let’s take a deep look at Rexxar. The three fundamental concepts are: **Route**, **Container**, **Web**.

First comes **Route**. In a Rexxar app, each page can be identified by a unique URI. We serve the URI mapping in the routes.json. Client should check the routes periodically via GET request and save the latest version in the cache. At runtime, while receiving a URI call, client asks for routes to determine which page is about to open. This process is called Route, here is how it works.

<img src="/img/introducing-rexxar/route.png" alt="Route" style="border:0;margin:40px auto;">

**Container**, as its name would suggest, is a container for WebView. It is responsible for serving HTML pages locally. Moreover, Container intercepts all requests from WebView. For static assets such as JavaScript and CSS files, Container caches them for faster access on the next visit. For API requests, Container runs Decorator, which can be used to modify HTTP requests like adding an authorization header. Finally, Container gives web pages access to native features. These are called Extensions. Extensions can be divided into two types: UI widgets and data services.

<img src="/img/introducing-rexxar/container.png" alt="Container" style="border:0;width:500px;margin:40px auto;">

The last part is **Web**. This part provides handy interfaces for developing Rexxar pages. We hides implementation detail of interaction with multiple platforms, so that developers just need to focus on product features. The way we write Rexxar pages is exactly the same as building SPAs: writing HTML & CSS, making request to fetch data, and rendering data to DOM. You can choose any front-end framework you like as there is no limit. Additionally, you can easily access extensions for native-like user experience.

Let me further elaborate on extensions. Each extension is designed to be cross-platform, while providing unified interface. The code you write just works perfectly on web platform. For implementation of UI widgets and data services, I’m going to give one example of each type.

This is a widget implementing pull-to-refresh:

<img src="/img/introducing-rexxar/widget.png" alt="Widget" style="border:0;width:500px;margin:40px auto;">

Another one is a GEO API:

<img src="/img/introducing-rexxar/data_api.png" alt="Data API" style="border:0;width:500px;margin:40px auto;">

## Workflow

What does the workflow from development to deployment looks like?

**Development** with Rexxar is easy, you set up a modern web application project as you usually do, and choose your favourite technology stack. At Douban, we are big fans of React.js. React is used with CSS-Modules to make reusable components. Meanwhile, we use webpack and gulp to process our modular projects, output HTML files and routes.json. As for debugging, start a local server and open pages in a browser like Chrome to inspect UI elements, test responsive design, etc. And even better, It is convenient to debug remotely, just switch to in-app mode, configure your application to use local routes.json and then you can check pages inside your application running on simulators or real devices. With hot module replacement enabled, you can reload your pages instantly every time you make a change without recompiling your app. It not only speed up your development times, but also helps designers quickly and easily review.

The key to **deployment** is to serve static HTMLs and the routes. Since Rexxar pages are static and don’t rely on server-side code, these HTMLs can be distributed instead of living on a server. Serving directly from a CDN unlocks speeds and performance. We take the following steps:
* Compiling source code, generating HTMLs and assets using our customized build system;
* Publishing them to CDN;
* Serving the routes.

The routes in JSON format is as follows:
```json
{
  "items": [
    {
      "remote_file": "https://path_to_cdn/dist/rexxar/demo-252452ae58.html",
      "uri": "douban://douban.com/rexxar_demo[/]?.*"
    }
  ],
  "partial_items": [
    {
      "remote_file": "https://path_to_cdn/dist/rexxar/demo-252452ae58.html",
      "uri": "douban://partial.douban.com/rexxar_demo/_.*"
    }
  ]
}
```

Notice that, after finishing development, you will need to fully test your changes before releasing it to your users. So, iterating on a pre-release branch is essential. Usually, QA engineers should tell you if it stable enough to publish.

<img src="/img/introducing-rexxar/deployment.png" alt="Deployment" style="border:0;">

## What’s Next

Rexxar brings the ease of web technologies into mobile application development and eliminates the cost and complexity of supporting multiple platforms. However, as we know, rendering with DOM is much slower than native components. Rexxar is not intent on being a replacement for iOS/Android development. After more than a year of experiment, we are convinced that it should be a great fit for your project in particular use cases. We are looking forward to your trial if it's right for the job, and eager to hear your feedback.

By now, we enjoy the advantages of flexible delivery and rapid iteration that had never been achieved before, yet there are issues to be solved. The infrastructure is still incomplete. We will continuously invest in infrastructure in order to improve developer experiences. Future works may include creating a better release system, error monitoring and crash reporting tools. As our projects grow large, it is also necessary to dig deeper into performance and parallel development.
