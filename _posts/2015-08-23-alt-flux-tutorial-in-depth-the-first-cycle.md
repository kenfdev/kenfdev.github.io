---
title: 'Alt Flux Tutorial in Depth &#8211; The First Cycle'
author: kenev
layout: post
permalink: /2015/08/23/alt-flux-tutorial-in-depth-the-first-cycle/
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
  - alt
  - alt-flux
  - flux
  - reactjs
---
Alt Flux Tutorial &#8211; Some Additional Information

# Why Alt???

I&#8217;ve started using [Alt][1] as my Flux library recently, and the only reason was that the type definition file (d.ts) for TypeScript was available &#8211; until then, I was using Reflux.

# Overview

Well, as I started using Alt by looking at the [tutorial][2], is was a bit hard for me to understand the unidirectional data flow. So I&#8217;ve decided to create a diagram to make it easier to depict what is happening. I&#8217;m not going in depth about the code and what is already written in the [Getting Started][3] section of the [official site][1] and I&#8217;m assuming that you have looked at the entire code. If any information in this post seems strange, feel free to leave some comments. It would extremely help me and whoever else reading this post.

## The Diagram

### Fetching the Location List

As I tried to write a data flow diagram for this process, I&#8217;ve noticed it was hard to write it as a unidirectional data flow because of the LocationSource. Anyway, the simplest diagram would probably look like this.

[<img src="/images/2015/08/Blank-UML-Alt.png" alt="-Blank UML - Alt" width="951" height="918" class="alignnone size-full wp-image-455" />][4]

This diagram does not quite illustrate how the code is written. The `Locations` React component calls `LocationStore`&#8216;s `fetchLocation` when the `componentDidMount`.

<pre><code class="js">// Locations.jsx
componentDidMount() {
  LocationStore.fetchLocations();
}
</code></pre>

But this `fetchLocations` method is not `LocationStore`&#8216;s method because it was automagically exported by calling `exportAsync(LocationSource)` **inside** `LocationStore`&#8216;s `constructor` &#8211; therefore, being able to call `LocationStore.fetchLocations()`. In addition, `LocationSource`&#8216;s `fetchLocations` method is called when `LocationStore.fetchLocations()` is called which is then connected to `LocationActions`&#8216;s `fetchLocations`. Therefore, I&#8217;ve simplified the diagram so that it *looks like* the react component&#8217;s initial `LocationStore.fetchLocations()` is calling the `LocationAction`&#8216;s `fetchLocations`, protecting the **Flux&#8217;s unidirectional architecture**.

#### In Depth

##### The View

First of all the `AllLocations` will get mounted and `LocationStore.fetchLocations();` gets executed.

<pre><code class="js">// Locations.jsx
var Locations = React.createClass({
  componentDidMount() {
    LocationStore.fetchLocations();
  },

  render() {
    // edited for brevity
  }
});
</code></pre>

##### The Action, Dispatcher, Store

As mentioned above, this call actually executes `LocationSource`&#8216;s `fetchLocations()`. This is because it was hooked (like a `mixin`) as explained in the [docs][5] inside `LocationStore`&#8216;s `constructor`.

<pre><code class="js">// LocationStore.js
class LocationStore {
  constructor() {
    // edited for brevity

    this.exportAsync(LocationSource); // &lt;- hooked here
  }
  // edited for brevity
}
</code></pre>

`LocationSource`&#8216;s `fetchLocations` makes an asynchronous call to the server (which is stubbed out in the tutorial as a `setTimeout` function) and returns a `Promise`. It also triggers the `LocationActions.fetchLocations` action to notify the `LocationStore` that it is going to load the locations.

<pre><code class="js">// LocationSource.js
var LocationSource = {
  fetchLocations() {
    return {
      remote() {
        return new Promise(function (resolve, reject) {
          // edited for brevity
          }, 250);
        });
      },

      // edited for brevity

      loading: LocationActions.fetchLocations // triggered action on fetch
    }
  }
}
</code></pre>

The diagram in detail would look like this.

[<img src="/images/2015/08/Blank-UML-Alt-View.png" alt="-Blank UML - Alt - View" width="902" height="507" class="alignnone size-full wp-image-457" />][6]

Now, the `LocationStore` listens to the `LocationActions.FETCH_LOCATIONS` hence, `handleFetchLocations` gets called &#8211; which resets the `this.locations` with an empty array.

<pre><code class="js">class LocationStore {
  constructor() {
    // edited for brevity

    // listening and registering event handlers
    this.bindListeners({
      handleUpdateLocations: LocationActions.UPDATE_LOCATIONS,
      handleFetchLocations: LocationActions.FETCH_LOCATIONS, 
      handleLocationsFailed: LocationActions.LOCATIONS_FAILED,
      setFavorites: LocationActions.FAVORITE_LOCATION
    });

    // edited for brevity
    //
    this.exportAsync(LocationSource);
  }

  // edited for brevity

  handleFetchLocations() {
    // resets the locations array on fetch
    this.locations = [];
  }

  // edited for brevity
}
</code></pre>

##### The View Again

As the `LocationStore`&#8216;s state changes, the `AllLocations` component receives the new `props` through the `AltContainer` automagically &#8211; see the [official documents][7] on what actually happens. The `LocationStore.isLoading()` returns `true` because the `LocationSource`&#8216;s `Promise` hasn&#8217;t resolved yet &#8211; and by the way, `LocationStore.isLoading` method is also automagically exposed via the `exportAsync` method &#8211; which then makes the `AllLocations` JSX end up as a `ajax-loader.gif`.

<pre><code class="js">var AllLocations = React.createClass({
  // edited for brevity

  render() {
    // edited for brevity

    // this returns true on fetch and before the promise gets resolved
    if (LocationStore.isLoading()) {
      return (
        &lt;div&gt;
          &lt;img src="ajax-loader.gif" /&gt;
        &lt;/div&gt;
      )
    }

    // edited for brevity
  }
});

var Locations = React.createClass({
  componentDidMount() {
    LocationStore.fetchLocations();
  },

  render() {
    return (
      &lt;div&gt;
        &lt;h1&gt;Locations&lt;/h1&gt;
        &lt;AltContainer store={LocationStore}&gt;
          // the AllLocations component automagically gets connected to the LocationStore
          &lt;AllLocations /&gt;
        &lt;/AltContainer&gt;

        &lt;h1&gt;Favorites&lt;/h1&gt;
        &lt;AltContainer store={FavoritesStore}&gt;
          &lt;Favorites /&gt;
        &lt;/AltContainer&gt;
      &lt;/div&gt;
    );
  }
});

</code></pre>

In a diagram in detail, the components and the store are connected as below.

[<img src="/images/2015/08/Alt-Flux-Alt-Store-and-Component.png" alt="Alt Flux - Alt - Store and Component" width="890" height="351" class="alignnone size-full wp-image-459" />][8]

Hence, the view ends up looking like this.

[<img src="/images/2015/08/27871be3fc49f8a80128c98910d3578a.png" alt="スクリーンショット 2015-08-23 21.26.07" width="381" height="446" class="alignnone size-full wp-image-460" />][9]

This is what happens in the first cycle. I&#8217;ll explain what happens after the `Promise` gets resolved in the next post.

 [1]: http://alt.js.org/
 [2]: https://github.com/goatslacker/alt-tutorial
 [3]: http://alt.js.org/guide/
 [4]: /images/2015/08/Blank-UML-Alt.png
 [5]: http://alt.js.org/docs/async/
 [6]: /images/2015/08/Blank-UML-Alt-View.png
 [7]: http://alt.js.org/docs/components/altContainer/
 [8]: /images/2015/08/Alt-Flux-Alt-Store-and-Component.png
 [9]: /images/2015/08/27871be3fc49f8a80128c98910d3578a.png
