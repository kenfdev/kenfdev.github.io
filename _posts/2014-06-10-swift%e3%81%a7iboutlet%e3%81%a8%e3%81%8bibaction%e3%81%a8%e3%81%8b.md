---
title: SwiftでIBOutletとかIBActionとか
author: kenev
layout: post
permalink: /2014/06/10/swift%e3%81%a7iboutlet%e3%81%a8%e3%81%8bibaction%e3%81%a8%e3%81%8b/
categories:
  - 未分類
---
The Swift Programming Languageを一通り読んでみたものの、意外とXcodeとどうやって連携していくかが書かれていないのでメモ。

<pre class="lang:default decode:true ">class MyViewController: UIViewController {
    @IBOutlet var button: UIButton
    @IBOutlet var textFields: UITextField[]
    @IBAction func buttonTapped(AnyObject) {
        println("button tapped!")
    }
}</pre>

通常の変数や関数定義の属性として先頭に@IBOutletや@IBActionくっつけるんですね。

英語ですが[ここ][1]にtipsが書いてあります。

 [1]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/WritingSwiftClassesWithObjective-CBehavior.html