---
title: 'これだけは覚えておこうES2015(旧ES6)【アロー(=>)関数】'
author: kenev
layout: post
permalink: /2016/04/19/es2015-es6-arrow-function/
s4_url2s:
  -
s4_image2s:
  -
s4_ctitle:
  -
s4_cdes:
  -
categories:
  - es2015
  - es6
tags:
  - es2015
  - es6
  - arrow-function
---

ES2015(ES6)の **これだけは覚えておいた方がいい！** シリーズ第二弾！前回は **let, const** についてやりましたが、今回は「アロー関数」について解説します！

# アロー関数（=>）

アロー関数、これは便利な文法の一つです。デメリットもちょっとありますが、まぁそこは目をつむりましょう！

# 結論

アロー関数があれば `that` やら `self` やらとは **基本的** にはおさらば！

 
{% highlight js %}
// ES5以前
// こんな書き方してたやつが
var that = this;
setTimeout(function() {
  that.doSomething();
});


// ES2015以降
// こんな感じになります！
setTimeout(() => this.doSomething());
{% endhighlight %}


# 解説

## `this`が自動的にBindされる！

コールバック関数などで今まで `this` がいなくなってしまうから、 `that` やら `self` やら、あるいは `bind` を自分で設定したりしてましたが、アロー関数のおかげでその必要がなくなります！ **宣言された場所の `this` が自動的にBindされるので、コードがだいぶすっきりするのです！

### コード例（ES5）

{% highlight js %}
'use strict';

var Person = (function() {
    var P = function(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    };
    
    P.prototype.greeting = function() {
        console.log('私の名前は...');
    
        setTimeout(function() {
          // thisがコールバックで消えちゃうので
          // undefinedになってしまう
          console.log(this.firstName + ' ' + this.lastName);
        }, 1000);
    }
    
    return P;
})()

var p = new Person('Ken', 'Fukuyama');

p.greeting();
// 私の名前は...
// （1秒後）
// undefined undefined
{% endhighlight %}

↑が典型的な例で、↓が典型的な修正方法

{% highlight js %}
P.prototype.greeting = function() {
    console.log('私の名前は...');
    
    var that = this; // thisを保持っておく
    setTimeout(function() {
      // thatにthisが保存されているので
      console.log(that.firstName + ' ' + that.lastName);
    }, 1000);
}

...
p.greeting();
// 私の名前は...
// (1秒後)
// Ken Fukuyama
{% endhighlight %}

あるいは直接bindする方法↓

{% highlight js %}
P.prototype.greeting = function() {
    console.log('私の名前は...');
    
    setTimeout(function() {
      console.log(this.firstName + ' ' + this.lastName);
    }.bind(this), 1000); // 関数自体をここのthisとbindさせる！
} 

...
p.greeting();
// 私の名前は...
// (1秒後)
// Ken Fukuyama
{% endhighlight %}

### コード例（ES2015）
ES2015なら↓って書きます！

{% highlight js %}
P.prototype.greeting = function() {
    console.log('私の名前は...');
    
    setTimeout(() => console.log(this.firstName + ' ' + this.lastName)
    , 1000); // thisはもうbindしてくれている！
} 

...
p.greeting();
// 私の名前は...
// (1秒後)
// Ken Fukuyama
{% endhighlight %}


## 注意！（これ結構重要）
便利そうに見える（いや、実際に便利ですけど）アロー関数ですが、なんでもかんでも使えばいいってわけじゃありません！ちゃんとこの仕組を理解しておかないと、思わぬ罠にはまったりします！例えば…

### callbackのthisがwindow以外だった場合
典型例はjQuery系のコールバックです。jQueryのオペレーターのコールバックの `this` は、DOMに結びついています。
例えば↓のようなDOMがあって

{% highlight html %}
<ul>
	<li>1</li>
	<li>2</li>
	<li>3</li>
</ul>
{% endhighlight %}

jQueryで↓のようにすると結果は

{% highlight js %}
$('li').each(function() {
  console.log(this.innerText);
});

// 結果
// "1"
// "2"
// "3"
{% endhighlight %}

これは、jQueryのコールバックの `this` はDOMに `bind` されているからなんです！なので `this` はそれぞれ `<li>` に該当しています。
これを何も考えずにアロー関数にしてしまうとエラーになるので注意！

{% highlight js %}
$('li').each(() => {
  console.log(this.innerText);
});

// "TypeError: Cannot read property 'innerText' of undefined
{% endhighlight %}


### 時と場合に応じて `this` を意図的に変えたい場合

誰が呼ぶかによって `this` を意図的に変えている場合も安易にアロー関数は使えません！
例えば↓の場合、 `greeting` をアロー関数にしてしまうと、 `this` がグローバルになってしまうので、 `this.name` がちゃんと評価されなくなってしまいます！



 
{% highlight js %}
// アロー関数なし
var obj = {
  name: 'Ken',
  greeting: function() {
    console.log('my name is ' + this.name);
  }
}

obj.greeting();
// 結果："my name is Ken"

// アロー関数あり
var obj = {
  name: 'Ken',
  greeting: () => {
    console.log('my name is ' + this.name);
  }
}

obj.greeting();
// 結果："TypeError: Cannot read property 'name' of undefined
{% endhighlight %}

# まとめ
結論としては、 **直感的に使えるようになる** 、と個人的には思えるんですけど仕様はしっかり覚えておかないと痛い目にあいますね！


# おまけ
`Babel` で上の例をトランスパイルした場合 `this` が一度 `_this` に保持してあるのが確認できます。

 
{% highlight js %}
// トランスパイル前
P.prototype.greeting = function() {
    console.log('私の名前は...');
    
    setTimeout(() => console.log(this.firstName + ' ' + this.lastName)
    , 1000); // thisはもうbindしてくれている！
} 

// トランスパイル後（_thisに保持っている）
P.prototype.greeting = function () {
    var _this = this;

    console.log('私の名前は...');

    setTimeout(function () {
        console.log(_this.firstName + ' ' + _this.lastName);
    }, 1000);
};

{% endhighlight %}

