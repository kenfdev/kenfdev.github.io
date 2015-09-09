---
title: 'React &#8211; PureRenderMixin Performance (Simple Overview)'
author: kenev
layout: post
permalink: /2015/03/08/react-purerendermixin-performance-simple-overview/
s4_url2s:
  - 
s4_image2s:
  - 
s4_ctitle:
  - 
s4_cdes:
  - 
categories:
  - react.js
tags:
  - javascript
  - Mixin
  - performance
  - PureRenderMixin
  - react
  - react.js
  - reactjs
---
# PureRenderMixin

React has a Mixin called [PureRenderMixin][1] which is included in the `addon` in the current version(0.12.2). The specification is as below.

> If your React component&#8217;s render function is &#8220;pure&#8221; (in other words, it renders the same result given the same props and state), you can use this mixin for a performance boost in some cases. 

## Overview

### Without PureRenderMixin

Let&#8217;s create a simple example without the PureRenderMixin. This changes (or just sets the same value in) the `value` `state` when the button is clicked.

When clicking the &#8216;Count Up Event&#8217; button, the `value` increments by 1 and &#8216;render&#8217; is called as well. In addition, when clicking the &#8216;Set Same Value Event&#8217; button, the render is being called despite the value not changing. This is meaningless and inefficient.

### With PureRenderMixin

So what happens if we add the `PureRenderMixin`? Let&#8217;s look at the example below.

You can see the difference when clicking the &#8216;Set Same Value Event&#8217; button. The `render` isn&#8217;t called this time.

### And？

What actually happened in the example above? Let&#8217;s dive into the code and see the implementation of PureRenderMixin.(Open Source is awesome :))

<pre><code class="js">var ReactComponentWithPureRenderMixin = {
  shouldComponentUpdate: function(nextProps, nextState) {
    return !shallowEqual(this.props, nextProps) ||
           !shallowEqual(this.state, nextState);
  }
};
</code></pre>

You can see that PureRenderMixin is implementing the &#8216;shouldComponentUpdate&#8217; and inside it, it compares the `props` and `state` using `shallowEqual`. The implementation of `shallowEqual` is going to be extremely important to remember. Let&#8217;s look at it.

<pre><code class="js">function shallowEqual(objA, objB) {
  if (objA === objB) {
    return true;
  }
  var key;
  // Test for A's keys different from B.
  for (key in objA) {
    if (objA.hasOwnProperty(key) &&
        (!objB.hasOwnProperty(key) || objA[key] !== objB[key])) {
      return false;
    }
  }
  // Test for B's keys missing from A.
  for (key in objB) {
    if (objB.hasOwnProperty(key) && !objA.hasOwnProperty(key)) {
      return false;
    }
  }
  return true;
}
</code></pre>

Summary of `shallowEqual`  
* If A and B&#8217;s value or reference is same, it returns `true`  
* Iff A&#8217;s property isn&#8217;t included in B, it returns `false`  
* If A&#8217;s property is in B and the value or reference differs, it returns `false`  
* If B&#8217;s property isn&#8217;t included in A&#8217;s, it return `false`  
* If none of the statements above match, it returns `true`

Let&#8217;s return to the button example above and look at the `handleNoChangeClick`. You can see that it is calling `setState` but is setting the same `value` as it had. (it&#8217;s not changing anything)

<pre><code class="js">handleNoChangeClick : function() {
  // intentionally set the same value
  this.setState({value : this.state.value});
}
</code></pre>

Hence, it is considered to be the **same value** and `shouldComponentUpdate` will be `false` which means it will not `render`.

### CAUTION

`PureRenderMixin` seems nice and will save some typing for us to write in the `shouldComponentUpdate`, but we ought to know the specifications clearly before we use it. Which is also written as a caution in the docs as follow.

> This only shallowly compares the objects. If these contain complex data structures, it may produce false-negatives for deeper differences. Only mix into components which have simple props and state, or use forceUpdate() when you know deep data structures have changed. 

　What this means could be explained in an example below.

This isn&#8217;t using `PureRenderMixin` and `setState` triggers `render` every time, which changes the value as intended despite the bad practice. Now let&#8217;s include the `PureRenderMixin` in the example below.

You can see that the &#8216;increment&#8217; button changes nothing. This is because of the `shallowEqual` specifications. It only compares the **value** or the **reference** and the array `values` hasn&#8217;t changed in this situation. Clicking the &#8216;Force Render&#8217; button will trigger the `render` and you can see the button clicked was actually incrementing the values.

# A Simple Performance Overview

## Without PureRenderMixin

### The Code

We&#8217;re going to continue using the examples above but we&#8217;re going to change it in order to use child components. 1 row will be 1 child component and every child component will manage how many times they have rendered.

### Results

You can see that clicking &#8216;increment&#8217; triggers all the component&#8217;s `render` and it&#8217;s pretty slow. (about 1.2 secs)

[<img src="/images/2015/03/cede3523c33c9c90dd2014ffa5b1abea-1024x751.png" alt="スクリーンショット 2015-03-08 14.40.42" width="540" height="396" class="alignnone size-large wp-image-300" />][2]

[<img src="/images/2015/03/10fbcadc6f42803a5cfb9c5ca61bbe46-1024x434.png" alt="スクリーンショット 2015-03-08 14.41.26" width="540" height="229" class="alignnone size-large wp-image-301" />][3]

## With PureRenderMixin

### The Code

　Now let&#8217;s add the `PureRenderMixin`.

　This time you can see that clicking &#8216;increment&#8217; only triggers the parent component and the target child component&#8217;s `render`. In addition, it only takes about 0.12 secs and it&#8217;s pretty much responsive.

[<img src="/images/2015/03/c9b7f3bcc1c724608f7a14efdd27cd7c-1024x748.png" alt="スクリーンショット 2015-03-08 14.41.58" width="540" height="394" class="alignnone size-large wp-image-303" />][4]

[<img src="/images/2015/03/f44f8855ad62d1ee4740bd6b7e465765-1024x434.png" alt="スクリーンショット 2015-03-08 14.41.31" width="540" height="229" class="alignnone size-large wp-image-302" />][5]

I&#8217;m not sure if the parent-child means of updating the `state` is following the FLUX way, but the examples above do show how `PureRenderMixin` will help you boost the performance (as the docs say). Seems very useful.

 [1]: http://facebook.github.io/react/docs/pure-render-mixin.html
 [2]: /images/2015/03/cede3523c33c9c90dd2014ffa5b1abea.png
 [3]: /images/2015/03/10fbcadc6f42803a5cfb9c5ca61bbe46.png
 [4]: /images/2015/03/c9b7f3bcc1c724608f7a14efdd27cd7c.png
 [5]: /images/2015/03/f44f8855ad62d1ee4740bd6b7e465765.png
