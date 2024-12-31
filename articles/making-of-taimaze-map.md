---
title: "【microCMS×Hono×Cloudflare Pages】自分が食べてきた台湾まぜそば店のマップアプリを作る"
emoji: "🍜"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["microcms", "hono", "cloudflarepages"]
published: false
---

# 自己紹介

台湾まぜそば研究家の [@mazetaro](https://zenn.dev/mazetaro) です。

名古屋発祥の汁なし麺料理「**台湾まぜそば**」の魅力を（勝手に）発信するために、2017年ごろから以下のような活動を行ってきました：

* テレビ番組への出演
  * 2019-09-10 [マツコの知らない世界](https://www.tbs.co.jp/matsuko-sekai/old/20190910.html)
  * 2023-02-17 [スッキリ](https://x.com/ntv_sukkiri/status/1626160713214627840)
* 学生サークル「[東京大学台湾まぜそば研究会](https://x.com/todai_taimaze)」（現在活動休止中）の立ち上げ
* [Instagram](https://www.instagram.com/taimaze.mazetaro/) へのレビュー投稿

残念ながら研究家としての活動だけで生計を立てることは難しいため…現在は会社員としてITの仕事をしながら細々と活動を続けています。

# 作ったもの

私が今までに食べてきた台湾まぜそば店のすべての記録をマップ上に可視化するアプリ「タイマゼマップ」を作りました。

https://map.taiwan-mazesoba.com/

<!-- TODO: ここに画像を貼る -->

もともと私が「マツコの知らない世界」に出演した際に、番組内でマツコ・デラックスさんに都内の台湾まぜそば事情をプレゼンするために開発したマップアプリがあり、放送後もしばらくは公開を続けていました。しかし、そのアプリは後述する事情のため保守・運用が極めて難しい状態になっていました。今回、技術スタックを一から見直して再構築を行い、今後も続けていけそうなめどが立ちました。その過程で得た知見を記事にまとめます。

# 技術スタック

バックエンド側のAPI・管理画面に microCMS を使い、フロントエンド側を Hono で React の SPA 構成にしたものを Cloudflare Pages で配信しています。

## microCMS

## Hono

yusukebe さんの記事を参考に、雑に React の SPA を作るために使いました。

https://zenn.dev/yusukebe/articles/06d9cc1714bfb7

マップ部分は react-map-gl を使っています。

https://github.com/visgl/react-map-gl

## Cloudflare Pages

# 感想
