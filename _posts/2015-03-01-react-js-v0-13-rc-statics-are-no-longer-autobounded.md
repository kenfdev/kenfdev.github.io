---
title: 'react.js v0.13 RC &#8211; statics are no longer autobounded'
author: kenev
layout: post
permalink: /2015/03/01/react-js-v0-13-rc-statics-are-no-longer-autobounded/
categories:
  - react.js
  - 未分類
tags:
  - arrow-function
  - autobound
  - component
  - javascript
  - react
  - react.js
  - statics
  - this
  - v0.13rc
---
> Static methods (defined in statics) are no longer autobound to the component class 

Components have *[statics][1]* and members defined in *statics* can be accessed directly via the component class. (refer to the official link for examples)

Before react.js v0.13rc, members defined in *statics* were autobound to the component which means &#8216;this&#8217; was tied to the component. This is better explained by the JSFiddle below.

MyComponent has message:string and staticMethod:function defined in *statics*. Inside the staticMethod, &#8216;this.message&#8217; is called. The point here is what &#8216;this&#8217; is bound to. Versions prior to v0.13rc &#8216;this&#8217; is bound to MyComponent which means &#8216;this.message&#8217; is the same as MyComponent.message, and therefore, the value is &#8216;Hello&#8217;. (Why not &#8216;Hey!&#8217; in spite of the &#8216;call&#8217; function using the &#8216;caller&#8217;? Because &#8216;this&#8217; is already bound to MyComponent and as far as I know, a method bounded cannot easily be overwritten)

Starting from v0.13rc, this magical *autobound* is not going to happen anymore. Hence the code above will end up outputting &#8216;Hey!&#8217;, not &#8216;Hello&#8217;.  
This is because staticMethod&#8217;s caller becomes the &#8216;caller&#8217; object.(not MyComponent). And the &#8216;caller&#8217; object has message:&#8217;Hey!&#8217; as his member.

I wonder why they made this change. Is it because i becomes more flexible??? Or because ES6 will let arrow functions autobind &#8216;this&#8217;??? That still remains a question for me.

 [1]: http://facebook.github.io/react/docs/component-specs.html#statics