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

#### このシナリオは3つのプロセスを持っています

* 検索: モデル名で検索
* 閲覧: モデルリストの閲覧
* 編集: 指定されたモデルの編集

上の3つのプロセスをチェインを使用し抽出してobjectに格納する。objectはネイティブscalaではsingletonです。  
あなたはobject専用のファイルか、同ディレクトリのSimulationファイルに作成する事ができます。

``` sample.scala
object Search {

  val search = exec(http("Home") // let's give proper names, as they are displayed in the reports
  .get("/"))
  .pause(7)
  .exec(http("Search")
  .get("/computers?f=macbook"))
  .pause(2)
  .exec(http("Select")
  .get("/computers/6"))
  .pause(3)

}

object Browse {

  val browse = ???

}

object Edit {

  val edit = ???

}

  /**  ??? can be used for marking methods that remain to be implemented.  
  *  @throws NotImplementedError  
  */  
  def ??? : Nothing = throw new NotImplementedError
```

  私たちのシナリオを、再利用シナリオなビジネスプロセスに書き換えます。

```business.scala
val scn = scenario("Scenario Name").exec(Search.search, Browse.browse, Edit.edit)
```
