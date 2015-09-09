---
title: SwiftでNSIndexPathを使う
author: kenev
layout: post
permalink: /2014/07/06/swift%e3%81%a7nsindexpath%e3%82%92%e4%bd%bf%e3%81%86/
categories:
  - Swift
tags:
  - NSIndexPath
  - Objective-C
  - Swift
  - UITableView
---
本業の方が鬼のように眼を酷使している為、最近はSwiftの勉強も滞っていますが久しぶりにちょっといじりました。

今回はObjective-Cの

<pre class="lang:objc decode:true">[NSIndexPath indexPathForItem:index inSection:0];</pre>

と等価になるSwiftのメソッドを探すのに手間をかけてしまったのでメモ。  
まず、ドキュメントを見るとObjective-Cのサンプルしか載っていない為手がかりがつかめません。ここのブリッジがまだ甘いので正式版リリースの頃にはもっとドキュメントが充実することを期待。

とりあえずはNSIndexPathの定義自体を覗いてみると

<pre class="lang:default decode:true">class NSIndexPath : NSObject, NSCopying, NSSecureCoding, NSCoding {
    
    init() /* designated initializer */
    init(indexes: CConstPointer&lt;Int&gt;, length: Int) /* designated initializer */
    
    convenience init(index: Int)
    
    func indexPathByAddingIndex(index: Int) -&gt; NSIndexPath!
    func indexPathByRemovingLastIndex() -&gt; NSIndexPath!
    
    func indexAtPosition(position: Int) -&gt; Int
    var length: Int { get }
    
    func getIndexes(indexes: CMutablePointer&lt;Int&gt;)
    
    // comparison support
    func compare(otherObject: NSIndexPath!) -&gt; NSComparisonResult // sorting an array of indexPaths using this comparison results in an array representing nodes in depth-first traversal order
}</pre>

たったのこんだけです。  
そしてObjective-Cと等価に見えるメソッドが見当たらなかったので、とりあえずコンストラクタinit(index: Int)に突っ込んで実行してみたら見事に実行時エラー。

**2014-07-06 22:24:58.914 MyStuff[13317:942088] \*** Terminating app due to uncaught exception &#8216;NSInternalInconsistencyException&#8217;, reason: &#8216;Invalid index path for use with UITableView.  Index paths passed to table view must contain exactly two indices specifying the section and row.  Please use the category on NSIndexPath in UITableView.h if possible.&#8217;**

「カテゴリ使えよ」と。あれ？ということはSwiftでいうextensionってことですよね。なるほど、ってなことでUIKit>UITableViewの中身を観てみるとちゃんとありました。

<pre class="lang:default decode:true">extension NSIndexPath {
    
    init(forRow row: Int, inSection section: Int) -&gt; NSIndexPath
    
    var section: Int { get }
    var row: Int { get }
}</pre>

これを使って以下のように書けば思った通り動作。Objective-Cの知識がそもそも無いので初歩的なところで結構つまづきますねー。

<pre class="lang:default decode:true">NSIndexPath(forRow: index, inSection: 0)</pre>

&nbsp;