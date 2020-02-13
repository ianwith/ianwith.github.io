title: Everything You Would Like to Know About WeChat Mini Program
date: 2020-02-12 20:00:00
tags:
  - front end
  - Mini Program
categories: work
---

## Product Positioning
WeChat Mini Programs are a new way of connecting users and services. They are easy to access and share on WeChat, delivering an excellent user experience.

## What are the differences between WeChat js-sdk, weui, and Mini Program.
[WeChat js-sdk](https://developers.weixin.qq.com/doc/offiaccount/en/OA_Web_Apps/JS_SDK.html) is a web development kit based on WeChat provided by WeChat public platform for web developers. By using WeChat js-sdk, web developers can effectively use the capabilities of WeChat to take photos, select pictures, voice, location and other mobile phone systems, and directly use WeChat sharing, scan, card coupons, payment and other WeChat-specific capabilities to provide better web experience for WeChat users. You can think of WeChat js-sdk as our LIFF.

[weui](https://weui.io/) is a UI library by WeChat official design team. weui has [an ordinary web (HTML/CSS/JS) version](https://github.com/Tencent/weui) and [a Mini Program version](https://github.com/Tencent/weui-wxss/). Basically, it contains only styles and has no functionality of WeChat js-sdk.

Mini Program is a more advanced solution, which takes one step forward from an ordinary mobile Web page. As you know, Mini Program provides a set of languages to let developers build more powerful apps within WeChat platform. The merits(from the user and developer perspective) are:
* Fast loading
* More powerful capabilities
* Native experience
* Easy and secure WeChat data exposure
* Efficient and simple development

For more details -- [About Mini Programs](https://developers.weixin.qq.com/miniprogram/en/dev/framework/quickstart/#About-Mini-Programs%5D(https://developers.weixin.qq.com/miniprogram/en/dev/framework/quickstart/%23About-Mini-Programs)

<!-- more -->

## How do you access Mini Programs?
From almost everywhere, e.g. a QR code from offline or online, WeChat's drag down view(recently visited and my favorites will be listed here), "Search" box of WeChat, "Discover" tab of WeChat, WeChat public/official accounts, a chat message someone shared with you.

<img src="/img/everything-you-would-like-to-know-about-wechat-mini-program/qrcode.jpg" alt="Access Mini Programs" style="width:260px">

## Steps from registering, developing to publishing your Mini Programs.

#### *1. Register*
First of all, you need to register a Mini Program Account on WeChat Official Accounts Platform official website. This is kind of like Apple Developer account, you can register as Individual or Company or other entity types.

#### *2. Mini Program information is perfect*
After registration, log in to the Mini Program admin console to complete the Mini Program's Information and prepare for development. (refer to step 1 in the picture)

<img src="/img/everything-you-would-like-to-know-about-wechat-mini-program/step2.png" alt="Step 2" style="width:600px">

#### *3. Develop Mini Programs*
Download Wechat DevTools. You can develop Mini Program using your own editor as you like, It's no different than normal web development. However, with Wechat DevTools you can develop and debug Mini Program easily. Here is what it looks like:

<img src="/img/everything-you-would-like-to-know-about-wechat-mini-program/step3.png" alt="Step 3" style="width:600px">

#### *4. Submit review*
After development, you can upload your code to Cloud by pressing the "upload" button in DevTools. Then go to the Mini Program admin console to see your uploaded versions, you can do versions management like: set v1.1.0 to beta preview, after preview is fine, submit v1.1.0 to WeChat team for review.

<img src="/img/everything-you-would-like-to-know-about-wechat-mini-program/step4.png" alt="Step 4" style="width:600px">

#### *5. Release it after passing review*
In my experience, the WeChat team's review takes approximately from two hours to half a day. After the code of a Mini Program is approved, just press the "Release" button and here we go.

## Technical Overview

#### Q: What does a typical Mini Program project consist of?
Here is a basic structure of Mini Program project.
```
├── app.js // app level logic
├── app.json // app level configurations for Mini Program
├── app.wxss // optional, common style sheet
├── pages
│   │── index // an index page
│   │   ├── index.wxml // template of index page
│   │   ├── index.js // javascript logic of index page
│   │   ├── index.json // page level configurations for index page
│   │   └── index.wxss // style sheet of index page
│   └── logs
│       ├── logs.wxml
│       └── logs.js
└── utils
```

#### Q: What configuration does the app.json file contain?
For example, you can determine page file paths and window behaviors, set the network timeout, and set multiple tabs. reference [configuration](https://developers.weixin.qq.com/miniprogram/en/dev/reference/configuration/app.html)

```json
{
  "pages": [ // The page path list. e.g. There are two pages in this Mini Program.
    "pages/index/index",
    "pages/logs/index"
  ],
  "window": { // Window is used to set the Mini Program status bar, navigation bar, titles, and window background colors.
    "navigationBarTitleText": "Demo"
  },
  "tabBar": { // Behavior of the tab bar at the bottom of the page.
    "list": [{
      "pagePath": "pages/index/index",
      "text": "Home"
    }, {
      "pagePath": "pages/logs/logs",
      "text": "Log"
    }]
  },
  "networkTimeout": {
    "request": 10000,
    "downloadFile": 10000
  },
  "debug": true,
  "navigateToMiniProgramAppIdList": [
    "wxe5f52902cf4de896"
  ]
}
```

#### Q: What does the app.js contain?
You can think of it as the entrance to run the Mini Program. Developers call App() method in app.js, which is used to register a Mini Program app instance. Each Mini Program has only one app instance. In App() method, you can link the lifecycle callback function, error listening function, page not found listening function, and the like.

```js
//app.js
App({
  onLaunch (options) {
    // Do something initial when launch.
  },
  onShow (options) {
    // Do something when show.
  },
  onHide () {
    // Do something when hide.
  },
  onError (msg) {
    console.log(msg)
  },
  ...
  globalData: 'I am global data'
})
```

#### Q: How do I write a page?
A page usually consists of four files: wxml + wxss + js +json. See the following example.

You should call Page() method in js file to register a page instance, then you specify the initial data of the page, the lifecycle callback function, the event handling function...

You should use WXML to define page structure. you might notice that the syntax is heavily inspired by VueJS.

Additionally, don't forget to use WXSS to define styles, and use json file to configure the window behaviors of this page.

```html
<!--wxml index.wxml-->
<view wx:if="{{view == 'WEBVIEW'}}"> WEBVIEW </view>
<view wx:elif="{{view == 'APP'}}"> APP </view>
<view wx:else="{{view == 'MINA'}}"> MINA </view>
<button bindtap="onMyOwnHandler">Try to change data</button>
```

```js
// index.js
Page({
  data: {
    view: 'MINA'
  }
    onLoad: function(options) {
    // Do some initialize when page load.
  },
  onReady: function() {
    // Do something when page ready.
  },
  onShow: function() {
    // Do something when page show.
  },
  ...
  onMyOwnHandler(item) {
    this.setData({ view: "WEBVIEW" })
  },
})
```

```css
/** index.wxss **/
@import "common.wxss";
view {
    font-size: 18px;
    color: #333333;
}
```

#### Q: What is WXML/WXSS?
WXML stands for WeiXin Markup Language. It’s a markup language invented by WeChat and can be used to build the page structure. [WXML reference](https://developers.weixin.qq.com/miniprogram/en/dev/reference/wxml/)

WXSS stands for WeiXin Style Sheets. It’s a set of style languages that describe WXML component styles. It has most of the features of CSS but also incorporates some new features and modifications.

#### Q: What components(WXML tags) can I use?
[official documents](https://developers.weixin.qq.com/miniprogram/en/dev/component/)
Please refer to the above documentation for a complete list of components.
The syntax of writing a component is consistent with writing HTML. A component comes with some features and basic WeChat styles, but developers have a lot of freedom to style their own components with WXML/WXSS, just like HTML/CSS. Basically, most components are rendered as the web elements(dom), some components are implemented as native components for better performance and user experience.
Give the following most frequently used components as example.
1. View Containers
- view (View Container, it’s just like div tag in HTML)
- scroll-view (A scrollable view container)
- swiper (A sliding view container)
2. Basic Components
- icon (A icon with WeChat style, you can specify color, types, size)
- progress (Progress bar)
- rich-text (Rich text is rendered by specifying HTML nodes, useful for articles or blogs)
- text (Simple text)
3. Form Components
- button
- checkbox
- input (serves as a native component only in focus)
- picker-view
- textarea (native component)
4. Navigators
- navigator
5. Media Components
- image
- audio
- video (native component)
- camera (native component)
6. Map (native component)
7. Canvas (native component)

```html
<view class=“section”>
  <view class=“section__title”>flex-direction: row</view>
  <view class=“flex-wrp” style=“flex-direction:row;”>
    <view class=“flex-item bc_green”>1</view>
    <view class=“flex-item bc_red”>2</view>
    <view class=“flex-item bc_blue”>3</view>
  </view>
</view>
```

#### Q: Can I customize my own component?
Yes, you can. If you compare WXML to HTML, you can think of custom components as web components. [custom-component reference](https://developers.weixin.qq.com/miniprogram/en/dev/framework/custom-component/wxml-wxss.html)

#### Q: Can web front-end code run in Mini Program? Conversely, can Mini Program code run in a web browser?
Unfortunately, both answers are NO. Although WXML/WXSS is similar to HTML/CSS at first glance, but they are still different. However, the transition from Web development to Mini Program development involves a low cost for front-end developers. Additionally, developers can use isomorphic frameworks/tools like [kbone](https://github.com/wechat-miniprogram/kbone) to make web code run in Mini Program without any changes.

#### Q: If I want to use functionalities like scanCode, Login, Network Request, WebSocket, WeChat Pay, getUserInfo, setNavigationBarTitle, setBackgroundColor, createAnimation, etc. How do I do that?

Yes, Mini Program provides plenty of APIs to complete these features. [API reference](https://developers.weixin.qq.com/miniprogram/en/dev/api/)

#### Q: How does Mini Program work in runtime? Is Mini Program running in a WebView?
The runtime environment of Mini Program can be divided into a rendering layer and a logic layer. WXML templates and WXSS styles are used in the rendering layer and JavaScripts logic in the logic layer. These two layers are managed via two threads: the rendering layer renders its interfaces using WebView threads, while the logic layer runs the JavaScripts with JsCore threads.

Basically, the two threads(WebView and JsCore) run in the same time, and when they need to communicate with each other, all the communication will be transferred via the WeChat app(refer to "Native" in the following picture). For example, when `setData` is called in js logic in order to update the page display with new data, WeChat app will be responsible for sending the data from JsCore thread to WebView thread.

<img src="/img/everything-you-would-like-to-know-about-wechat-mini-program/runtime.png" alt="Runtime" style="width:600px">

The underlying implementations of the Logic Layer and Rendering Layer vary by environment.

| Runtime Environment | Logic Layer    | Rendering Layer                               |
| ------------------- | -------------- | --------------------------------------------- |
| iOS	                | JavaScriptCore | WKWebView                                     |
| Android	            | V8m            | Chromium custom kernel(self-developed engine) |
| WeChat DevTools     | NWJS           | Chrome WebView                                |

#### Q: Why Mini Program uses this kind of two-threads technic instead of just using WebView?
We know that, in web front-end development, rendering threads and scripting threads are mutually exclusive, that is to say, UI rendering and JavaScripts run alternately in the same thread, so long-time script running may make a page unresponsive.

So Mini Program uses a two-thread model in order to make the page respond faster. Many optimizations have been made to Mini Program to reduce data transfer between the two threads. Meanwhile, the rendering layer at the bottom of the page stack is gradually collected when the number of layers becomes high.

On the other hand, the pattern of running Mini Program code in separate threads provides better security performance, e.g. displaying open-data components in a manner that ensures user privacy.

#### Q: What is the mechanism of Mini Programs for caching and updating?
For caching, when a user first launches a Mini Program, all resources related to the user interface are downloaded to WeChat’s local storage. As this increases the storage space occupied by WeChat, the maximum size of a single file package that developers can upload is limited to 2MB, with the total size of all packages not exceed 8 MB. In such a case, developers are encouraged to build Mini Program into different sub-packages and load them as needed.

For updating, Case 1: sometimes Mini Program updates when it is deactivated, WeChat app will check at several time points if the new version of Mini Program exists locally, if yes, the Mini Program will be automatically updated to the latest version in next launch. It may take at most 24 hours to send the new version data to all the users(Updates timing is uncertain, in my experience, it takes only minutes). Case 2: sometimes Mini Program checks updates when cold startup, if a new version exists, the code package of the new version will be downloaded asynchronously and it will be started with the local client package, which means the new version of the Mini Program will not be applied until the next cold startup.

#### Q: What are the limitations of developing Mini Program?
The functionality provided by default Mini Program framework is powerful, most of UI and features that can be implemented on the web applications can be implemented on Mini Program.

However, to be honest, the development experience of Mini Program is not so good at first. Due to some technical restrictions like the absence of DOM APIs and limited language set, developers are not able to use modern front-end libraries like jQuery, React, etc.

#### Q: I don't want to use the default framework, is there any other choice?
Yes, thanks to the active community, we now have dozens of frameworks built for you to write Mini Programs using modern web languages. Here are some examples: TinaJS(simply based on the default framework), Wepy(modern, in a component way), MPVue(for VueJS developers), Taro(for React developers). Taro is the one I use the most, it lets you write Mini Program entirely in React way, and the app code you write can be compiled to Web, WeChat Mini Program or even for react native apps(with single code base).
Here is a good article about frameworks: [WeChat mini programs — which development framework choose in 2018?](https://medium.com/le-wagon/wechat-mini-programs-which-development-framework-choose-in-2018-6ae493d6fea0)
