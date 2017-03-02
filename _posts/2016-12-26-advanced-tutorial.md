---
layout: post
title:  "Advanced Tutorial"
date:   2016-12-26 19:00:00 +0900
categories: gatling
---
Advanced Tutorial

このセクションは、[クイックスタート](http://gatling.io/docs/2.2.3/quickstart.html#quickstart)と基本的なシミュレータでの動作を
すでに終了していることを想定しています。
ここでは、より高度な内容のDSL構造で一連のリファクタリングを適用していきます。

### Step 01: Isolate processes(プロセスを分離する)

今の私達のシミュレーションは、一つの大きなシナリオです。

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
```

<div style='color:red'>Tips</div>

``` tips.scala
/**  ??? can be used for marking methods that remain to be implemented.  
*  @throws NotImplementedError  
*/  
def ??? : Nothing = throw new NotImplementedError
```

  私たちのシナリオを、再利用シナリオなビジネスプロセスに書き換えます。

```business.scala
val scn = scenario("Scenario Name").exec(Search.search, Browse.browse, Edit.edit)
```

### step2 仮想ユーザーの設定

ここまではいいですかね。一人のユーザーで負荷テストができます。
ユーザーを増やしてみましょう

２種類のユーザーを定義しましょう：

+ 一般ユーザー: 検索,閲覧ができる

+ 管理ユーザー: 検索と閲覧と編集ができる

シナリオに変換してみましょう:

```scenario.scala
val users = scenario("Users").exec(Search.search, Browse.browse)
val admins = scenario("Admins").exec(Search.search, Browse.browse, Edit.edit)
```

シミュレートユーザー数を増やすには、以下のようにシミュレーションの設定を変更するだけです

```setup.scala
setUp(users.inject(atOnceUsers(10)).protocols(httpConf))
```

わずか10人のユーザーしかセットしていません、なぜなら、テストサンプルのwebアプリケーションだからです。
親切心でサーバをクラッシュさせないでね;-)

3000ユーザーのシミュレーションをしたいけど、同時には開始したくないかもしれません。
確かに、実際のユーザーは徐々にwebアプリケーションに接続にきますね。

gatlingでは ``` rampUsers ``` で上記を再現しています。
ランプ値はユーザーが線形的に増える期間を示しています。

ここでは10人の一般ユーザーと2人の管理ユーザーを10秒で線形的に増やすので、サーバを破壊したりしません。

```setup.scala
setUp(
  users.inject(rampUsers(10) over (10 seconds)),
  admins.inject(rampUsers(2) over (10 seconds))
).protocols(httpConf)
```

### Step 03: Use dynamic data with Feeders and Checks  
(FeedersとChecksで動的データを使ってみよう)

私たちは一連のユーザーを実行するようシミュレーションしましたが、しかしそれは全て同一のモデルを検索します。

でもユーザーはそのような操作をしません。ユーザーは異なるモデルを検索できた方がいいですよね?

全てのユーザーが同じシナリオでプレイしないように動的データが必要であり、  
ライブシステムとはまったく異なる動作になります。(キャッシュする。JIT etc...)  
この場合、Feedersを使うと有効です。

Feedersは全ての値を含むデータソースです。Feedersは色々な型があり、もっともシンプルな型はCSV Feedersです。  
CSV Feedersを私たちのシナリオ内で一度テストしてみましょう。

``` user-files/data ```フォルダに``` search.csv ```という名前で作成してみましょう。

このファイルは以下の行を含んでいます。

```search.csv
searchCriterion,searchComputerName
Macbook,MacBook Pro
eee,ASUS Eee PC 1005PE
```

feeder変数を宣言し、上のデータを複数ユーザーに使用してみましょう。

```Search.scala
object Search {

val feeder = csv("search.csv").random // 1, 2

val search = exec(http("Home")
	.get("/"))
	.pause(1)
	.feed(feeder) // 3
	.exec(http("Search")
	.get("/computers?f=${searchCriterion}") // 4
	.check(css("a:contains('${searchComputerName}')", "href").saveAs("computerURL"))) // 5
	.pause(1)
	.exec(http("Select")
	.get("${computerURL}")) // 6
	.pause(1)
}
```
