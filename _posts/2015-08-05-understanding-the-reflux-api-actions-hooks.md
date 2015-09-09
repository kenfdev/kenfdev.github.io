---
title: 'Understanding the Reflux API &#8211; Actions Hooks'
author: kenev
layout: post
permalink: /2015/08/05/understanding-the-reflux-api-actions-hooks/
s4_url2s:
  - 
s4_image2s:
  - 
s4_ctitle:
  - 
s4_cdes:
  - 
categories:
  - flux
  - react.js
tags:
  - emit
  - event
  - flux
  - reactjs
  - reflux
---
Action hooks are called before the action&#8217;s event gets emitted.

  * `preEmit`
  * `shouldEmit`

could be used. `shouldEmit` can control whether or not to emit the event before it actually gets called. It&#8217;s probably going to be pretty handy when you want to throttle events. Let&#8217;s see it in action by using it in the [previous sample][1] I posted.

<div class="oembed-gist">
  <noscript>
    View the code on <a href="https://gist.github.com/kenfdev/35141c897839a2ad7b74">Gist</a>.
  </noscript>
</div>

`preEmit` is just logging what kind of arguments it received. `shouldEmit` on the other hand, controls whether or not to emit the event. In this case, the `load` action emits an event only if the `Load Success` button is pressed.

Check it out on my [JSBin][2]

 [1]: http://kenev.net/2015/08/04/understanding-the-reflux-api-asynchronous-actions/
 [2]: https://jsbin.com/teyibe/edit?js,console,output