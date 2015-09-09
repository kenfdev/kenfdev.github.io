---
title: 'React &#8211; PureRenderMixinの検証'
author: kenev
layout: post
permalink: /2015/03/08/react-purerendermixin%e3%81%ae%e6%a4%9c%e8%a8%bc/
s4_url2s:
  - 
s4_image2s:
  - 
s4_ctitle:
  - 
s4_cdes:
  - 
categories:
  - JavaScript
  - React.js
  - Web-dev
tags:
  - component
  - flux
  - javascript
  - Mixin
  - performance
  - PureRenderMixin
  - react
  - react.js
  - reactjs
  - render
  - setState
  - shallowEqual
---
# PureRenderMixin

## はじめに

　Reactには[PureRenderMixin][1]という今のところ(0.12.2) addonとして提供されているMixinがあります。どういうものかと言うと

> If your React component&#8217;s render function is &#8220;pure&#8221; (in other words, it renders the same result given the same props and state), you can use this mixin for a performance boost in some cases.
> 
> 対象となるコンポーネントが「純粋」（renderが同じ`props`と`state`を与えられて必ず出力結果が同じになる）であれば、パフォーマンス向上が見込めるmixin。 

と本家で述べられています。

## シンプルな仕様検証

### PureRenderMixin無し

　さっそくこれを実際に試してみます。以下、`PureRenderMixin`無しで`value`という`state`をボタンのクリックでインクリメントまたは同一値を設定（`setState`）します。

　&#8217;Count Up Event&#8217;ボタンをクリックした場合は`value`の値が変わりますし、それに伴って`render`も呼ばれています。そして、&#8217;Set Same Value Event&#8217;ボタンをクリックした場合も`value`が変わらないにも関わらず`render`が呼ばれているのがわかります。これは**無駄**な処理が走っていることになります。

### PureRenderMixin有り

　では、`PureRenderMixin`を追加するとどうなるのか？以下はMixinを追加したバージョンです。

　違いがわかるのは&#8217;Set Same Value&#8217;ボタンをクリックしたときです、`render`が呼ばれていないことがわかります。

### どうなってるの？

　PureRenderMixinが何をしているのか気になるところです。オープンソースなので簡単に観ることができます。

<pre><code class="js">var ReactComponentWithPureRenderMixin = {
  shouldComponentUpdate: function(nextProps, nextState) {
    return !shallowEqual(this.props, nextProps) ||
           !shallowEqual(this.state, nextState);
  }
};
</code></pre>

　`shouldComponenUpdate`を使って、`props`と`state`に対して等値であるかどうか比較しています。等値の比較方法には`shallowEqual`を使っている様ですね。この`shallowEqual`の仕様がとても重要なポイントです。

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

  * AとBが同一値、同一参照先であれば`true`
  * AのプロパティがBになかったら`false`
  * AのプロパティがBにあって、値または参照先が違ってたら`false`
  * BのプロパティがAになければ`false`
  * 上のどれにもあてはまらなければ`true`

　ここで、先ほどの例に戻った場合に&#8217;Set Same Value Event&#8217;ボタンは`handleNoChangeClick`を実行し、その中では`setState`を呼んでいるけど、元の`this.state.value`を`value`に設定していることに注目してください。

<pre><code class="js">handleNoChangeClick : function() {
  // intentionally set the same value
  this.setState({value : this.state.value});
}
</code></pre>

　これは**同一値**として扱われるので`shouldComponentUpdate`にて`false`扱いとなり、`render`が走らないのです。

### 要注意事項

　いちいち自分で書かなくても済むようになるのでとても便利そうに見えるPureRenderMixinですが、仕様ははっきりと理解しておかないとまずそうです。本家にも赤字でしっかりと書いてあります。

> This only shallowly compares the objects. If these contain complex data structures, it may produce false-negatives for deeper differences. Only mix into components which have simple props and state, or use forceUpdate() when you know deep data structures have changed.
> 
> あくまでもオブジェクトを比較する際には浅い比較しか行いません。複雑な構造（深い構造）をもったオブジェクト比較を行った場合は意図しない結果を生む可能性があります。単純な`props`や`state`を持つコンポーネントにだけ使うか、深い階層で更新があったことがわかった場合は`forceUpdate()`を使ってください。 

　どういうことかと言うと、以下のような極端なコードを書いてみます。（下記のようなコードを書くことなんてありえませんが、ぱっといい例が思い浮かばなかったのでやむを得ず。）

　これは`PureRenderMixin`を含めていないので`setState`を呼ぶ度に自動的に`render`が走り、ボタンに対応した値が更新されていくのが確認できます。それではこれに`PureRenderMixin`を含めてみましょう。

　&#8217;increment&#8217;ボタンをクリックしても値が変わりませんね。これが&#8217;shallowEqual&#8217;の仕様です。比較するのは「値」か「参照先」が一緒かどうかです。`values`配列の中の値を変えているのですが、`values`自体の「参照先」は変えていません。よって、`PureRenderMixin`はrenderする必要が無いと思い込んでしまい、再描画が走らないのです。何回か&#8217;increment&#8217;をクリックして&#8217;Force Render&#8217;ボタンをクリックしたら一気に値が変わるのが確認できるかと思います。

# シンプルなパフォーマンス検証

## PureRenderMixin無し

### コード

　それではPureRenderMixin無しでシンプルなパフォーマンス検証をしてみましょう。先ほどと同じような例ですが、今回は１行１子コンポーネントとして作り直します。また、便利のためImmutableJSも使いました。5,000行使って各行の値を更新するボタンを用意しています。また、各々の子コンポーネントに何回`render`が走ったかわかるようにカウンタをつけています。

### 結果

&#8216;increment&#8217;ボタンを押す度に全ての親・兄弟コンポーネントも再描画が走っているのがわかるし**もっさり**しているのが体感できると思います。パフォーマンスも再描画に1.1秒ほどかかっているのがわかります。

[<img src="/images/2015/03/cede3523c33c9c90dd2014ffa5b1abea-1024x751.png" alt="スクリーンショット 2015-03-08 14.40.42" width="540" height="396" class="alignnone size-large wp-image-300" />][2]

[<img src="/images/2015/03/10fbcadc6f42803a5cfb9c5ca61bbe46-1024x434.png" alt="スクリーンショット 2015-03-08 14.41.26" width="540" height="229" class="alignnone size-large wp-image-301" />][3]

## PureRenderMixin有り

### コード

　上の例で`PureRenderMixin`を有効にします。

　今回は&#8217;increment&#8217;ボタンを押した場合は親とその特定の子コンポーネントのみが再描画されているのがわかります。また、パフォーマンスも0.18秒と、飛躍的に向上しています。

[<img src="/images/2015/03/c9b7f3bcc1c724608f7a14efdd27cd7c-1024x748.png" alt="スクリーンショット 2015-03-08 14.41.58" width="540" height="394" class="alignnone size-large wp-image-303" />][4]

[<img src="/images/2015/03/f44f8855ad62d1ee4740bd6b7e465765-1024x434.png" alt="スクリーンショット 2015-03-08 14.41.31" width="540" height="229" class="alignnone size-large wp-image-302" />][5]

　このコンポーネントの親子関係と`state`の更新方法がFLUXの思想に則っているのか疑問が残っていますが、PureRenderMixinの検証という意味では方向性はさほど間違っていないかと思います。これを念頭に置いて今後の開発に活かしていきたいですね。

 [1]: http://facebook.github.io/react/docs/pure-render-mixin.html
 [2]: /images/2015/03/cede3523c33c9c90dd2014ffa5b1abea.png
 [3]: /images/2015/03/10fbcadc6f42803a5cfb9c5ca61bbe46.png
 [4]: /images/2015/03/c9b7f3bcc1c724608f7a14efdd27cd7c.png
 [5]: /images/2015/03/f44f8855ad62d1ee4740bd6b7e465765.png
