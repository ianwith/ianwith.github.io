title: Modular and Reusable CSS
date: 2017-09-29 22:21:32
tags:
  - work
  - front end
  - summary
  - css
  - architecture
  - component
categories: work
---

This article is about the CSS Architecture. Writing modular CSS is always the most important, but also difficult thing. I believe in ideas that a good CSS Architecture should be predictable, reusable, maintainable, and scalable. The way how we structure our CSS has evolved over the years. We can write powerful and maintainable CSS nowadays with the power of open source community. I have tried several different approaches, The following are some of them.

<!-- more -->

Before we get started, Let’s take a look at the big picture about structuring web pages. At the very first, based on how things actually works in Browser, we separated our pages in three layers: Content - HTML, Presentation - CSS, Behaviour - JS. It’s hard to say this is still the best practice. We now have new standards and tools help us make modules and components, based on product’s features.

<img src="/img/modular-and-reusable-css/example_block.png" style="border:0" alt="Example block">

I would like to take this block for demonstration through this article.

## OOCSS or SMACSS or BEM

They are probably the earliest CSS methodologies been proposed to bring order to large CSS files. These three concepts share common goals, though they have different emphasis and take different principles and approaches. Each of them is more of a guideline than rule for writing CSS.

**OOCSS** refers to Object-oriented CSS. It’s based on two main principles:

1. Separate structure and skin
2. Separate container and content

For the first one, the structure is about the layout of elements, while skin defines what they look like. Properties include width, height, margin, padding are the layout. Others like font, color are the skin.

For the second one, containers can be represented by classes which define the object structure. Content are often nestled within containers. Content elements refer to elements such as images, paragraphs.

The objective of OOCSS is reusing styles. So that the classes should not dependent on particular elements. However, it might increase the number of classes attached to a single element. The code may looks like:

```html
<div class="media">
  <div class="image image-small image-loaded">
    <img src="" />
  </div>
  <div class="content">
    <p></p>
  </div>
</div>
```

**SMACSS** stands for Scalable and Modular Architecture for CSS. The core of SMACSS is categorizing CSS rules. There are five types of categories: Base, Layout, Module, State and Theme. Each category has certain guidelines.

For me, state rules are a little bit confusing. According to author of SMACSS, state rules are ways to describe how our modules or layouts will look when in a particular state. Is it hidden or expanded? Is it active or inactive? The Author also offers a naming convention for working with these rules, for example, l- prefix for Layout rules, is- as in is-hidden for State.

SMACSS is an overall guide for organizing all your CSS rules, from layout to state. This is a short example:

```html
<div class="media">
  <div class="media-image is-loaded">
    <img src="" />
  </div>
  <div class="media-content">
    <p></p>
  </div>
</div>
```

**BEM** is the most popular of the three originated at Yandex. It stands for Block, Element, Modifier. The idea is to divide the user interface into independent blocks. The most common rule we used for naming BEM entities follow the structure `block-name__element-name--modifier-name`. This is called Two Dashes style. There are also alternative naming styles. The most important thing is, make entity names unambiguous and easy to understand.

With BEM, we are able to quickly understand the relationship between elements. It helps everyone working on the same project share syntax and code. In my practice, sometimes naming of classes itself might be a difficult thing.

```html
<div class="media">
  <div class="media__image media__image--loaded">
    <img src="" />
  </div>
  <div class="media__content">
    <p></p>
  </div>
</div>
```

The above three which are frequently mentioned together can be applied on their own or combined with other methodologies. Each of these approaches defines convention for writing structured CSS, but neither of them solves the problem that all class names live in the global scope. The conventions relies on the developer to conform to, and it's difficult in large projects.

## CSS Modules

With JavaScript Module ecosystem grows rapidly these years. We now have tools to solve the CSS global naming problem. Here comes CSS Modules.

> A CSS Module is a CSS file in which all class names and animation names are scoped locally by default.

Using CSS Modules means adding a process in build step, usually with webpack and css-loader, that generate a global unique name for each CSS class.

The top benefit of CSS Modules is that, it gives us a new way to structure our project. Instead of organizing CSS and JS separately, we break down pages into components. Components can be formed of it’s own JS and CSS, which are local-scoped and reusable.

I started using CSS Modules a year ago. It helps me break styles down into pieces, yet it’s composable, and makes writing isolated components easier. Since all styles are scoped to particular component. There is no global namespace pollution and no more conflicts. I am inspired a lot and learned the way to build UI by switching to Component-Based Thinking.

```
- components
  - MediaImage
    - index.js
    - index.css
  - MediaContent
    - index.js
    - index.css
```

## styled-components

styled-components is one of the best CSS-in-JS solutions. With styled-components, you are able to write actual CSS rules in a JS file, instead of converting to JS objects. It looks something like this:

```jsx
const Media = styled.div`
  display: block;
  /* ... */
`
const MediaImage = styled.div`
  display: inline-block;
  ${props => props.loaded && css`
    opacity: 1
  `}
  /* ... */
`
const MediaContent = styled.div`
  display: inline-block;
  /* ... */
`

const MediaBlock = () => (
  <Media>
    <MediaImage loaded={true} />
    <MediaContent />
  </Media>
)
```

styled-components started at the point that, removing class names. If you are using React and CSS-Modules, you do this `<div className={css.mediaImage)}></div>`. This `className` plays the role of a mapping from stylesheets to elements. Removing the mapping let us focus on styling each single component, without worrying about naming classes, and those styles are tightly coupled with their components. Moreover, CSS in styled-components are more than just plain CSS, it is enhanced with features like nesting, dynamically adapting based on props, etc. Generally speaking, if you are putting CSS in JavaScript, you will have access to sharing variables and functions between CSS and JS, no more class names or separate CSS files, easy to modify a component’s style when state changes.

Recently, I shared my experience with styled-component. In my latest project, it helps me think of each element as a component, and that makes my component smaller and smaller. Instead of many CSS selectors and HTML tags, I have tiny reusable components. Then I plug them together to build complex user interfaces.

Some of my colleagues think that having two languages in one file is often a problem. I would prefer making components closer to their styles. Although separating CSS or not depends on your file organization style, structuring based on components is undoubtedly the most elegant way. Keep this in mind, always choose a technology that makes your code understandable and reusable before you start crafting.
