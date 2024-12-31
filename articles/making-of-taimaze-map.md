---
title: "【microCMS×Hono×Cloudflare Pages】自分が食べてきた台湾まぜそば店のマップアプリを作る"
emoji: "🍜"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["microcms", "hono", "cloudflarepages"]
published: false
---

# 自己紹介

台湾まぜそば研究家の [@mazetaro](https://zenn.dev/mazetaro) です。

名古屋発祥の汁なし麺料理「**台湾まぜそば**」の魅力を（勝手に）発信するために、2017年ごろから活動してきました。以下は活動の一例です：

* テレビ番組への出演
  * 2019-09-10 [マツコの知らない世界](https://www.tbs.co.jp/matsuko-sekai/old/20190910.html)
  * 2023-02-17 [スッキリ](https://x.com/ntv_sukkiri/status/1626160713214627840)
* 学生サークル「[東京大学台湾まぜそば研究会](https://x.com/todai_taimaze)」（活動休止中）の立ち上げ
* [Instagram](https://www.instagram.com/taimaze.mazetaro/) へのレビュー投稿

残念ながら研究家としての活動だけで生計を立てることは難しいため…😂 会社員としてITの仕事をしながら細々と活動を続けて現在に至ります。

# 作ったもの

私が今までに食べてきた台湾まぜそば店のすべての記録をマップ上に可視化するアプリ「タイマゼマップ」を作りました。

https://map.taiwan-mazesoba.com/

![](/images/overview-taimaze-map.png)

もともと私が「マツコの知らない世界」に出演した際、番組内でマツコ・デラックスさんに都内の台湾まぜそば事情をプレゼンするために開発したマップアプリ（以下、**旧アプリ**）が存在していました。しかし、旧アプリは後述する事情のため実際の運用が困難な状態になっていました。今回、技術スタックを一から見直して再構築し、今後も続けていけそうなめどが立ちました。その過程で得た知見を記事にまとめます。

# 技術スタック

バックエンド側のAPI・管理画面に microCMS を使い、フロントエンド側を Hono で React の SPA 構成にしたものを Cloudflare Pages でホストしています。

## microCMS

microCMS はヘッドレスCMSを提供するサービスです。今回は店舗一覧APIを作成し、そこに各店舗のレビューを登録していきました。

![](/images/example-taimaze-admin.png)
*（新アプリ）microCMS で作った店舗レビューの管理画面*

旧アプリでは Google スプレッドシートに店舗情報を一行ずつ手打ちで入力し、そのシートをデータベースに見立てて Google Apps Script (GAS) によって JSON で配信する API を作って利用していました。

![](/images/old-taimaze-admin.png)
*（旧アプリ）Google スプレッドシートで管理していた店舗一覧*

この方式はすぐ使える API を用意する分には良かったのですが、以下の点が運用上のネックでした：

* 記入する列や行がずれるだけで API が壊れてしまう
* 途中まで記入して下書きとして保存できない
* 画像をドライブにアップロードしてから、その URL をスプレッドシートに転記する手間がかかる

バックエンド部分を microCMS に置き換えたことで、これらの問題をすべて解決できました！

## Hono

yusukebe さんの記事を参考に、雑に React の SPA を作るために使いました。

https://zenn.dev/yusukebe/articles/06d9cc1714bfb7

マップ部分は react-map-gl を使っています。

https://github.com/visgl/react-map-gl

## Cloudflare Pages

アプリは Cloudflare Pages でホストしています。

[Preview deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/) の機能を使って開発中のブランチの挙動を見ながら、メインブランチにマージする流れで開発を進めました。

![](/images/preview-url.png)
*PRを作るとプレビュー環境の URL がコメントで展開される様子*

フロントエンド側は microCMS のコンテンツを参照しかしない作りにしたことで、この Preview deployments 機能との相性が良かった（開発中のコードの不具合がコンテンツの整合性を破壊する心配が一切なかった）と感じています。

# 感想

当初はバックエンド側のAPI・管理画面も自分で実装するつもりでしたが、インフラ管理や認証機構の導入も含めて考えると結構な工数がかかるため、なかなか踏み出せずにいました。microCMS を採用したことで本当に注力したいフロントエンド側の実装に集中でき、本業の合間の限られた時間のなかでリリースに成功したと思います。

ヘッドレスCMSはブログのようなサービスに使うものという固定観念があったのですが、ニッチなユースケースでも柔軟にカスタムして適用できる点がメリットだと認識しました。
