---
title: ui-router使用時のUnit Test注意事項
author: kenev
layout: post
permalink: /2015/01/31/ui-router%e4%bd%bf%e7%94%a8%e6%99%82%e3%81%aeunit-test%e6%b3%a8%e6%84%8f%e4%ba%8b%e9%a0%85/
categories:
  - JavaScript
  - TypeScript
  - Web-dev
tags:
  - AngularJS
  - karma
  - typescript
  - ui-router
---
ui-router使用時にunit testを行ったらよくわからないエラーがkarmaさんから伝えられて割とはまりました。

# 現象

下記のようにmyAppにhomeというstateを用意していたとします。

<pre><code class="ts">angular.module('myApp').config(function ($stateProvider, $urlRouterProvider) {
    $urlRouterProvider.otherwise('/');
    $stateProvider.state('home', {
        url: '/',
        templateUrl: 'home.html',
        controller: 'HomeCtrl as vm'
    });
});
</code></pre>

そしてこのアプリに以下の様なserviceがあったとして

<pre><code class="ts">class UsersSvc {
  constructor(private $http:ng.IHttpService) {}
  fetch():ng.IHttpPromise&lt;IUser&gt; {
    return this.$http.get('/url/to/users');
  }
}
angular.module('myApp').service('UsersSvc', UsersSvc);
</code></pre>

テストコードを以下のように書きます。「UsersSvcのfetchメソッド実行したら１個ユーザが返ってくるよね」という簡単なテストです。

<pre><code class="ts">describe('usersSvc', function () {
    beforeEach(module('myApp'));
    var UsersSvc, $httpBackend;
    beforeEach(inject(function (_UsersSvc_, _$httpBackend_) {
        UsersSvc = _UsersSvc_;
        $httpBackend = _$httpBackend_;
    }));

    afterEach(function () {
        $httpBackend.flush();
    });

    describe('#fetch', function () {
        beforeEach(function () {
            $httpBackend.expect('GET', '/url/to/users')
                .respond([{id: 'someUserId'}])
        });
        it('gets 1 user', function () {
            UsersSvc.fetch().success(function (users) {
                expect(users).to.have.length(1);
            })
        });
    });
});
</code></pre>

簡単なテストなのでパスするかと思いきや「karma start」するとエラーが出てきます。

<pre><code class="bash">PhantomJS 1.9.8 (Mac OS X) usersSvc "after each" hook FAILED
        Error: Unexpected request: GET home.html
        Expected GET /url/to/users
</code></pre>

原因は  
[Karma test breaks after using ui-router][1]  
にて説明されています。  
どうやら$stateサービス使用時には、初期化の段階でtemplateの読み込みを試みるみたいです。今回の場合は

<pre><code class="ts">$stateProvider.state('home', {
    url: '/',
    templateUrl: 'home.html',
    controller: 'HomeCtrl as vm'
});
</code></pre>

上記の設定が行われている為にhome.htmlに対して知らないうちにGETが発行されているんですね。そしてこのGETが実際に実行されるのがテストケースの中のafterEach内で行われている$httpBackend.flush()のときです。これが

<pre><code class="ts">beforeEach(function () {
    $httpBackend.expect('GET', '/url/to/users')
        .respond([{id: 'someUserId'}])
});
</code></pre>

とURLが不一致になる為、テスト失敗になっちゃうんですね。

# 対処法

## $templateCacheに設定

手動でこの問題を回避する方法に$templateCacheに該当するtemplateを設定することです。今回のケースでいけば下のように$templateCache.putを使ってhome.htmlを設定できます。$templateCacheに設定しておくことでGETが発行されなくなるという仕組みです。（多分）

<pre><code class="ts">beforeEach(inject(function (_UsersSvc_, _$httpBackend_, $templateCache) {
    UsersSvc = _UsersSvc_;
    $httpBackend = _$httpBackend_;
    $templateCache.put('home.html','&lt;div&gt;blank or whatever&lt;/div&gt;');
}));
</code></pre>

## karma-ng-html2js-preprocessorで自動化

[karma-ng-html2js-preprocessor][2]という便利なツールがあります。指定したテンプレを$templateCacheに自動で設定してくれます。  
まずはインストールしましょう。

<pre><code class="bash">npm install --save-dev karma-ng-html2js-preprocessor
</code></pre>

次にkarma.conf.jsの設定です。以下、ポイントとなる部分しか記述していません。実際のkarma.conf.jsはテストをするための諸々の設定が必要です。また、ngHtml2JsPreprocessorの設定も各環境に合わせて設定してください。筆者の場合「www/」の直下にしかtemplateを置いていないので以下のような設定になっています。

<pre><code class="ts">module.exports = function (config) {
    config.set({
        preprocessors: {
            'www/*.html': ['ng-html2js']
        },

        files: [
            'www/*.html',
        ],

        ngHtml2JsPreprocessor: {
            // ファイルパスの先頭から取り除く文字列
            stripPrefix: 'www/',
            // ファイルパスの末尾から取り除く文字列
            //stripSufix: '.ext',
            // パスの先頭に付加する文字列
            //prependPrefix: 'served/',

            // or define a custom transform function
            //cacheIdFromPath: function(filepath) {
            //    return cacheId;
            //},

            // moduleNameを指定することでこのモジュールを読み込むだけでtemplateを全て読み込むことが可能
            moduleName: 'templates'
        },
    })
};
</code></pre>

ここまで設定が完了しましたらテスト時にmoduleを読み込むだけでテストは意図通りに実行されるようになります。

<pre><code class="ts">beforeEach(function () {
    module('myApp');
    module('templates'); // ここを追加
});
</code></pre>

 [1]: https://github.com/angular-ui/ui-router/issues/212
 [2]: https://github.com/karma-runner/karma-ng-html2js-preprocessor