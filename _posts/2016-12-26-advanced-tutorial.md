---
layout: post
title:  "Advanced Tutorial"
date:   2016-12-26 19:00:00 +0900
categories: gatling
---
Advanced Tutorial

このセクションは、[クイックスタート](http://gatling.io/docs/2.2.3/quickstart.html#quickstart)と基本的なシュミレータでの動作を
すでに終了していることを想定しています。
ここでは、より高度な内容のDSL構造で一連のリファクタリングを適用していきます。

### Step 01: Isolate processes(プロセスを分離する)

今の私達のシュミレーションは、一つの大きなシナリオです。

最初に、Seleniumのページオブジェクトパターンに似た、ビジネスプロセスに分割します。
これによりメンテナンスを犠牲にすることなく、一部の部品を簡単に再利用して複雑な動作を構築することができます。

