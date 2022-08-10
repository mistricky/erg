# ブランチの命名と運用方針

* main, beta, ver-*はread-onlyブランチである。直接のコミットは受け付けず、下流ブランチからのマージによってのみ更新される。
* 基本的に開発は`dev`ブランチ一本で行う。どうしてもブランチを切らないと作業しにくい場合のみ`feature-*`ブランチか`issue-*`ブランチを作成する。

## major

* 最新のメジャーリリース
* 以下の条件を満たしたbetaをマージする

* コンパイルが成功する
* 全てのテストが成功する
* インストーラを用意する

## beta

* 最新のベータリリース
* 以下の条件を満たしたdevをマージする

* コンパイルが成功する
* 全てのテストが成功する

## main

* メイン開発ブランチ

* コンパイルが成功する

## ver-*

* 過去のメジャーリリース
* majorを切って作る
* 深刻なバグが見つかった場合はブランチごと削除する

## feature-*

* 特定の一機能を開発するブランチ
* mainを切って作る

* 条件なし

## issue-*

* 特定のissueを解決するブランチ

* 条件なし