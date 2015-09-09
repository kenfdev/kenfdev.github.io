---
title: '[MarionetteJS→React 002] 基本的なmodelのViewへの表示'
author: kenev
layout: post
permalink: /2015/07/31/marionettejs%e2%86%92react-002-%e5%9f%ba%e6%9c%ac%e7%9a%84%e3%81%aamodel%e3%81%aeview%e3%81%b8%e3%81%ae%e8%a1%a8%e7%a4%ba/
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
  - Marionette
  - Marionette2React
  - React.js
  - TypeScript
  - Web-dev
tags:
  - marionette
  - model
  - property
  - react
---
# MarionetteJS

MarionetteJSでmodelを作って、それをItemView内に設定し、Viewにmodelのプロパティを表示するものを作ります。  
ソースコードの差分は[こちら][1]

`ContactView`は`ItemView`で、templateには`#contact-template`を使用しており、かつそのtemplateでは

<pre class="lang:default decode:true ">&lt;%- firstName %&gt; &lt;%- lastName %&gt;</pre>

&nbsp;

という形式で`ContactView`に設定されているmodelのプロパティを表示しています。これもMarionetteJSの基本的な使い方ですね。

# React

ReactではComponentに「この値を使ってくれぃ」ってな感じにComponent呼び出し側からオブジェクトを渡すことができます。Componentに渡した値はそのComponent内で`this.props`配下でアクセスすることができるのです。とりあえずはTypeScriptも使ってることですし、今回のサンプルデータであるAliceを作ってみると下みたいになります。

<pre class="lang:js decode:true ">interface IContact {
  firstName: string,
  lastName: string,
  phoneNumber: string
}

// using interface rather than class for simplicity
let alice: IContact = {
  firstName: 'Alice',
  lastName: 'Arten',
  phoneNumber: '555-0184'
}
</pre>

&nbsp;

簡単の為、classは使わずにinterfaceだけ準備しました。  
まだルートComponentしか扱っていないので、`React.createElement`を使ってelementを生成します。この場合には第二引数に渡したオブジェクトがComponent内で`this.props`でアクセスできるようになるのです。  
Componentは以下のようになります。

<pre class="lang:js decode:true ">class ContactManager extends React.Component {
  render () {
    return &lt;p&gt;
    {this.props.firstName + ' ' + this.props.lastName}
    &lt;/p&gt;
  }
}
</pre>

Componentを見たらだいたいどんな役割をもってるのかわかって頭のなかでもVisual化しやすいなー、と個人的には思います。

[<img class="alignnone size-medium wp-image-418" src="http://kenev.net/wp-content/uploads/2015/07/8cfef8d371574190256a66ba0115f2e6-300x116.png" alt="スクリーンショット 2015-07-31 1.07.11" width="300" height="116" />][2]

めでたしめでたし。

React側のソース差分は[こちら][3]

 [1]: https://github.com/davidsulc/marionette-gentle-introduction/commit/a5a36d3ffc6e3a71c29bee95082ba0269b70927e
 [2]: http://kenev.net/wp-content/uploads/2015/07/8cfef8d371574190256a66ba0115f2e6.png
 [3]: https://github.com/kenfdev/reactjs-gentle-introduction/commit/22f5faeb1f36d4ef3c3e0f156af44cc489677973