---
layout: post
title:  "Gatling quick start docs"
date:   2016-11-18 19:00:00 +0900
categories: jekyll update
---
Gatling quick start

## Introduction

このセクションではシンプルなクラウドwebサーバで基本的なDSLを書いてロードテストをします。

## Getting the bundle

gatlingのzipは[ここ](http://gatling.io/#/resources/download)

## Installing

ダウンロードしたパッケージをunzipして好きなフォルダーにおいてください。

#### warning

windows users : Programsフォルダにガトリングを置いてしまうと、Permissionで問題がでる可能性があります。

gatlingを順番に実行しましょう。まずはJDKをインストールする必要があります。gatlingにはJDK8以上が必要ですが、JDKの最新バージョンを使用することを推奨しています。
OSのチューニングに関する全ての詳細は[Operations](http://gatling.io/docs/2.2.3/general/operations.html#operations)こちらを参照してください

#### warning

gatlingの起動スクリプトとmavenで起動する場合は ```JAVA_HOME``` をOSの環境変数に設定してください。
OSXのように独自のやり方でjavaの実行バージョンを確認することができる ``` java -version ```、なのでOSXを使用している場合は
異なのバージョンのjavaが実行されるかもしれません。もし ``` Unsupported major.minor version 51.0 ``` このエラーに遭遇したら
JDK8に対する ``` JAVA_HOME ``` を設定してください。


* In order 順番に

* regarding ~に関する

## ファイルエンコーディングに関して
gatlingはUTF-8が標準です。もし別のエンコーディングを使用したい場合は、

* レコーダーを使用している場合は、適切なエンコーディングを選んでください
* ```gatling.conf```で適切なエンコーディングを設定しシミュレーションのコンパイル時に使用され、
リクエストとレスポンスで実行されます。
* あなたのテキストエディターのエンコーディングが適切に設定されているか確認してください。

## Scalaコードに関して
gatlingシミュレーションは[Scala](http://www.scala-lang.org/)で記述します。だけど心配しないで。
Scalaの事を多く知らなくても基本的な機能を使う事ができます。

ほとんどの場合、gatlingのDSLであなたのやりたい事シミュレーションが作れます。  
もし、さらにScalaに興味があれば、[Twitter’s Scala School.](http://twitter.github.io/scala_school)をおすすめします。

### <span style="color: #ADD8E6; ">Note</span>

**このドキュメントを読んで**もしヘルプが必要であれば、   
無料で[Google Group](https://groups.google.com/forum/#!forum/gatling)に参加しましょう。

## Test Case

このページではgatlingのほぼすべての特徴を説明します。シミレーション、シナリオ、フィーダ、レコーダ、繰り返し処理 などなど

## お試しで使用するアプリケーション

このチュートリアルでは、Computer-Database という名前のアプリケーションを使います。 URL [http://computer-database.gatling.io.](http://computer-database.gatling.io)
このアプリケーションはシンプルなCRUD機能をもつコンピュータを管理するもので、このsampleは[Play Framework](https://www.playframework.com/) の バージョン2.3 で作られている。

## シナリオ

このアプリケーションでパフォーマンスのテストをするには、ユーザが操作する時に実際に何が起こるかを表すシナリオを作成しましょう。
実際のユーザがこのように操作すると考えています。
1.ユーザがアプリケーションに本文する
2.macbookと検索する
3.関連する１のモデルを見つけ、開く(addされているので複数見つかります。)
4.ホームに戻る
5.ページを反復する
6.新しいモデルを作る

## 基本

