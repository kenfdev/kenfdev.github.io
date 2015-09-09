---
title: Error establishing a database connection
author: kenev
layout: post
permalink: /2013/12/18/error-establishing-a-database-connection/
categories:
  - MySQL
  - wordpress
tags:
  - mysql
  - mysql.sock
---
連投。

というのもWordpressを久しぶりにいじってさぁページをローカルでチェックしてみようと思ったら表題の文言が出てWordpressがうんともすんとも返事をしてくれないではありませんか。

とりあえず

mysql -u root -p

でどうなってるのかチェックしようとしたところ、

ERROR 2002 (HY000): Can&#8217;t connect to local MySQL server through socket &#8216;/var/lib/mysql/mysql.sock&#8217; (111)

上の文言がターミナルに表示されてそもそもMySQLが反応しない。  
確かにこれではWordpressも何もしようがありません。

ではmysqlの再起動はどうだ？と思って

service mysqld restart

を実行してみるも

Another MySQL daemon already running with the same unix socket.

と表示されて再起動も不可。  
よし、こうなったらグーグル先生に聞くしか無いってことで聞いてみたら[この記事][1]が見つかりました。

ということでとりあえず下記で問題のファイルを移動。

mv /var/lib/mysql/mysql.sock /var/lib/mysql/mysql.sock.bak

そういえばmysqlを叩いた時にもこのファイル名がなんとかかんとかって出てましたね。  
あっさりこれで解決。

よく思い出してみたら、そういえばこの前突如私のMBPが再起動を起こしていました。VMWare上で動いてるCentOSも中途半端な状態で急に切れたことになるので、その関係で上のファイルがちゃんと閉じなかった(?)か何かなんでしょうね。

 [1]: http://fnya.cocolog-nifty.com/blog/2013/12/mysql-another-m.html