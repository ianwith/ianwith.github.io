title: Shadow DOM Styling Notes
date: 2020-03-18 21:39:32
tags:
  - front end
  - css
  - Web Components
  - Shadow DOM
categories: work
---

Recently I'm making a company-wide Web Component library. Here are some notes regarding styling. I'm going to use `<l-header-bar>` component as an example.

## Note 1: The Shadow DOM boundary (shadow root) prevents styling coming in or going out

Web components can isolate styles via the Shadow DOM, which is called scoped CSS:

- CSS selectors from the outer page don't apply inside your component.
- Styles defined inside don't bleed out. They're scoped to the host element.

CSS selectors used inside shadow DOM apply locally to your component. In practice, this means we can use common id/class names again, without worrying about conflicts elsewhere on the page. Simpler CSS selectors are a best practice inside Shadow DOM. They're also good for performance.

Meanwhile, outside CSS selectors are not able to access the HTML element inside Shadow DOM. The following code won't work.

```html
<!-- l-header-bar template -->
<header>
...
</header>
<style>
header {
    background: red;
}
</style>
```
```html
<!-- user's HTML -->
<l-header-bar></l-header-bar>
<style>
l-header-bar > header {    /* No Way! */
    background: blue;
}
</style>
```

## Note 2: Custom properties go through the shadow DOM

Styles inside shadow DOM can access CSS variables even from outside.
According to this, One way to custom style is to provide hooks in the form of CSS variables and let user use these variables.

For example, inside `l-header-bar`:
```html
<!-- l-header-bar template -->
<header>
...
</header>
<style>
header {
    background: var(--background);
}
</style>
```
In user's HTML, they can do this.
```html
<!-- user's HTML -->
<l-header-bar></l-header-bar>
<style>
l-header-bar {
    --background: blue;
}
</style>
```

## Note 3: A slot is globally stylable

A slot element style is defined outside the Web Components.

```html
<!-- l-header-bar template -->
<header>
    <slot name="action-left"></slot>
</header>
```

In user's code, they can completely control the action-left slot style(which is a customized button in the following case). ← This allows our components to be flexible.

```html
<!-- user's HTML -->
<l-header-bar>
    <button slot="action-left">User Customized Button</button>
</l-header-bar>
<style>
button {
/* style the button as they want */
}
</style>
```

## Note 4: A slot can also take styles defined inside the shadow DOM.

Inside Shadow DOM, we can use `::slotted(<compound-selector>)` to style nodes that are distributed into a `<slot>`. So this allows us to put additional styles to user's slots. However, we can only select top-level slotted nodes.

```html
<!-- user's HTML -->
<l-header-bar>
    <div slot="title" class="my_title">
        LINE NEWS, <span class="icon">Icon</span>
    </div>
</l-header-bar>
```
```html
<!-- l-header-bar template -->
<style>
<slot name="title"></slot>
::slotted(.my_title) {
  color: red;
}
/* DOESN'T WORK (can only select top-level nodes).
::slotted(.icon),
::slotted(.my_title .icon) {
  color: blue;
}
*/
</style>
```

## Note 5: Inherited properties will be inherited as usual.

Take a look at the following code from the user side to see how the properties affect style inside `l-header-bar`.

```html
<!-- user's HTML -->
<div>
    <l-header-bar></l-header-bar>
</div>
<style>
    div {
        color: black;   /* color goes through the l-header-bar, e.g. the text color inside l-header-bar is inherited from this. */
        font-size: 16px;/* font-size also goes through the l-header-bar. */
        display: block; /* display is not a inheritable property so setting this does not make the display property of l-header-bar also become block. */
        backgound: red; /* backgound is not a inheritable property so setting this does not make the background property of l-header-bar also become red. But since background property defaults to transparent, so in act, l-header-bar can get a red background. */
    }
    /* However, user can set style of l-header-bar element directly. The code below would work */
    l-header-bar {
        display: block;  /* user need to set display: block here because display property won't inherit parents' display value. Custom elements are display: inline by default. */
        background: blue;/* l-header-bar element's background property will be set to blue. */
    }
</style>
```

However, `l-header-bar` can prevent external inheritable styles from affecting the interior, by setting `all: initial;`.

```html
<!-- l-header-bar template -->
<header>
    <h1>Title</h1>
</header>
<style>
:host {
    all: initial;   /* subsequent properties are reset! So inheritable styles from outside cannot cross the shadow boundary */
}
h1 {
/* If we reseted to initial, the font-size and color here will be initial value of h1. Otherwise, it will inherit (16px black) from the outside div. */
}
</style>
```

## Note 6: For styling the component itself (Shadow DOM host): Outside styles always win over styles defined within Shadow DOM

The user is free to style the component element itself because it's just light dom.

Rules in the parent page have higher specificity than `:host` rules defined in the element. That is, outside styles win. This allows users to override your top-level styling from the outside. Also, `:host` only works in the context of a shadow root, so you can't use it outside of shadow DOM.

```html
<!-- l-header-bar template -->
<header>
...
</header>
<style>
:host {
    width: 100%;
}
</style>
```

```html
<!-- user's HTML -->
<l-header-bar></l-header-bar>
<style>
l-header-bar {
    width: 90%; /* This wins! l-header-bar element's width is 90%. */
}
</style>
```

## Note 7: `:host(<selector>)` and `:host-context(<selector>)`
```html
<!-- user's HTML -->
<l-header-bar></l-header-bar>
<l-header-bar disabled></l-header-bar>
<l-header-bar attr="sth"></l-header-bar>
<l-header-bar class="blue"></l-header-bar>
<body class="darktheme">
    <l-header-bar></l-header-bar>
<body>
```
```html
<!-- l-header-bar template -->
<style>
:host {
/* style <l-header-bar> element. */
}
:host(:hover) {
/* style when <l-header-bar> has hover. */
}
:host([disabled]) {
/* style when <l-header-bar> has disabled attribute. */
}
:host([attr="sth"]) {
/* style when <l-header-bar> attribute attr is "sth". */
}
:host(.blue) {
/* color <l-header-bar> when it has class="blue" */
}
:host-context(.darktheme) {
/* style <l-header-bar> when it's a descendant of .darktheme */
}
</style>
```
