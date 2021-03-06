---
layout: post
title:  "Gatling docs"
date:   2016-11-04 19:00:00 +0900
categories: gatling
---
Gatling

Gatlingは高度なロードテストツールです。それは簡単に使用できるようデザインされています。ハイパフォーマンスで保守しやすいです。
ガトリングは独創的で、素晴らしいHttpプロトコルをサポートしています。
複数のHttpサーバーでロードテストを行うには、最適なツールです。
コアエンジンとして、どんなプロトコルにも依存しません。どんなプロトコルでも完全に実装をサポートします。
例えば、Gatlingは現在もJMS (JavaMessageService)をサポートしています。

[クイックスタート](http://gatling.io/docs/2.2.3/quickstart.html#quickstart)はとても重要な概要が載っています。
HTTPのロードテストのためのシンプルなシナリオを始めよう。

シナリオはコードで定義され、私達がガトリングを効率的に作成するためには２つの方法がある。
シナリオはわかりやすいDSLで表現します。これらはバージョン管理が容易です。

ガトリングのアーキテクチャは、
HTTPなどのノンブロッキングで実装されているプロトコルは、非同期です。

この種のアーキテクチャにより、専用スレッドの代わりに仮想ユーザーをメッセージとして実装できます。
したがって、すごく少ないリソースで何千もの仮想ユーザーで実行しても問題ありません。

* instead of の代わりに
* dedicated 専用
* Thus したがって

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
