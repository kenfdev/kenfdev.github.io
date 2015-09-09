---
title: '[deprecated] I18n.enforce_available_locales の対処方法'
author: kenev
layout: post
permalink: /2014/01/05/deprecated-i18n-enforce_available_locales-%e3%81%ae%e5%af%be%e5%87%a6%e6%96%b9%e6%b3%95/
categories:
  - Rails
tags:
  - i18n
  - locales
  - Rails
---
Rails 4.0.2にアップグレードしてから下記のような警告が出てくることがあると思います。

> [deprecated] I18n.enforce\_available\_locales will default to true in the future. If you really want to skip validation of your locale you can set I18n.enforce\_available\_locales = false to avoid this message.

Stack Overflow の「[Rails I18n validation deprecation warning][1]」や<a href="http://qiita.com/shu_0115/items/1cf6220758a9c64c7adf" target="_blank">こちら</a>の記事で対処方法が記載されているので要チェックです。

Simone Carlettiさん、FabioさんによるStack Overflowの回答を、自身の理解を深める為にも和訳してみます。  
（素人の和訳なので自己責任で解釈してください。間違いがある場合コメント等で指摘していただければ幸いです）

重要： アプリでI18n 0.6.8を使っていないことを確認してください。（<a href="https://github.com/svenfuchs/i18n/commit/2893ebf4674a12fcded5779ad13fa05dc74dbf5e" target="_blank">バグ</a>ってます）

**回答（簡略バージョン）**  
警告を消すためにはapplication.rbのRails::Application内に以下の行を追加してください。

    config.i18n.enforce_available_locales = true
    

  * false（従来通りの挙動にする）  
    localeのバリデーション無視  
    というかlocaleなんてどうでもいい
  * true（今後のRailsのデフォルト挙動）  
    不正なlocaleが指定された場合にエラーを発生させたい  
    localeは気にしておきたい

※注意※

  * `config.i18n.default_locale等のi18n設定をする人はc<code>onfig.i18n.enforce_available_localesの設定の後に記述するようにしてください`</code>
  * サードパーティのgemがI18nを使用している場合に上記の対処方法だけでは警告が消えない可能性があるので、その場合はI18n.config.enforce\_available\_localesを設定してください

例

    require File.expand_path('../boot', __FILE__)
    
    # ...
    
    module YouApplication
      class Application < Rails::Application
    
        # ...
    
        config.i18n.enforce_available_locales = true
        # サードパーティgemが競合する場合は↓を使う
        I18n.config.enforce_available_locales = true
    
        # ...
    
      end
    end

&nbsp;

**回答（詳細バージョン）  
**警告はRails 4 (>= 4.0.2) and Rails 3.2 (>= 3.2.14)で表示されます。その理由は<a href="https://github.com/svenfuchs/i18n/commit/3b6e56e06fd70f6e4507996b017238505e66608c" target="_blank">このコミット</a>に記述されています。

> *Enforce available locales*  
> 利用可能なlocaleに遵守する
> 
> *When `I18n.config.enforce_available_locales` is true we&#8217;ll raise an I18n::InvalidLocale exception if the passed locale is unavailable.*  
> I18n.config.enforce\_available\_localesがtrueの場合に利用可能でないlocaleが渡された場合はI18n::InvalidLocale exceptionが発生します
> 
> *The default is set to `nil` which will display a deprecation error.  
> *デフォルトはnilに設定されており、警告が表示されます
> 
> *If set to `false` we&#8217;ll skip enforcing available locales altogether (old behaviour).*  
> falseに設定している場合はlocaleの遵守はさせません（既存の挙動）
> 
> *This has been implemented in the following methods :*  
> 以下のメソッドにて実装されています

  * > I18n.config.default_locale=

  * > I18n.config.locale=

  * > I18n.translate

  * > I18n.localize

  * > I18n.transliterate

上記修正が入る前は、サポートしていないlocaleが渡された場合に/config/locales内に存在するlocaleであればRailsが勝手にそのlocaleに切り替えていました。存在しない場合はconfig.i18n.default_localeに設定されているlocaleになっていました。（通常であれば:en）

新しいバージョンのI18nは開発者にもっとlocaleについて意識させるようになっています。

移行期間として警告は開発者に対してlocaleの挙動を明確に設定するよう促しています。

既存の挙動通りに動かす場合は以下の設定

    config.i18n.enforce_available_locales = false

そうでない場合はtrueに設定して、これからのRailsのデフォルト挙動に準拠するようにしてください。  
（デフォルトlocaleへの暗黙的な切り替えは無くなります）

★警告★

  1. config.i18n.default_localeや上で示したメソッド（`default_locale=`, `locale=`, `translate等）を使用している場合はconfig.i18n.enforce_available_localesの後に記述するようにしてください。そうでなければ警告は出続けます。`
  2. サードパーティgemがI18nの機能を使用している場合は上記対処だけでは不十分かもしれません。  
    これは優先順位がからんでくる問題なのですが、Railsアプリでconfigを設定した場合にはこの値はすぐにI18n　gemに渡されるわけではありません。Railsはconfigをそれぞれ内部的なオブジェクトに格納して、依存関係にある（gem等）をロードしてから該当クラスにconfigを引き渡します。gemがI18nのメソッドをconfigの値を受け取る前に使用する為、警告が出てしまうのです。  
    この場合I18nのconfigに直接設定値を渡す必要があります。方法は↓</p> 
        I18n.config.enforce_available_locales = true
    
    ちなみに先程までの設定は↓
    
        config.i18n.enforce_available_locales = true
    
    どのgemが原因となっているか探す為には以下の方法が効果的です  
    I18n　gemの場所を探してi18n.rbを開いてください。enforce\_available\_locales!メソッド内にpp callerという1行を追加してください。
    
    <pre class="lang:ruby decode:true "># Raises an InvalidLocale exception when the passed locale is not
# included in I18n.available_locales.
# Returns false otherwise
def enforce_available_locales!(locale)
  pp caller
  handle_enforce_available_locales_deprecation

  if config.enforce_available_locales
    raise I18n::InvalidLocale.new(locale) if !locale_available?(locale)
  end
end</pre>
    
    上記のように記述することでstacktraceを追うことができるので、どのgemが呼び出しているか辿ることが可能です。（私はAuthlogicが原因でした）↓
    
        ["/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/i18n-0.6.9/lib/i18n.rb:150:in `translate'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/i18n/translator.rb:8:in `translate'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/i18n.rb:79:in `translate'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/email.rb:68:in `validates_format_of_email_field_options'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/email.rb:102:in `block in included'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/email.rb:99:in `class_eval'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/email.rb:99:in `included'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/base.rb:37:in `include'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/base.rb:37:in `block in acts_as_authentic'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/base.rb:37:in `each'",
         "/Users/weppos/.rvm/gems/ruby-2.0.0-p247@application/gems/authlogic-3.1.0/lib/authlogic/acts_as_authentic/base.rb:37:in `acts_as_authentic'",
         "/Users/weppos/Projects/application/app/models/user.rb:8:in `<class:User>'",
         "/Users/weppos/Projects/application/app/models/user.rb:1:in `<top (required)>'",

 [1]: http://stackoverflow.com/questions/20361428/rails-i18n-validation-deprecation-warning