# ポートフォリオ（制作中）

# 自己紹介や経験のサマリー

# 技術スキル

## プログラミング言語

### C#

- 趣味のゲーム開発で使っており、1 年間経験があります。日常的に利用できます。
- 現在は C# を使い、一人で 2D のコロニービルダーゲームを作っています。
- 言語に提供されてる機能は概ね把握しており、問題なく活用できます。
  - 継承, 例外, イテレーター, デレゲート, generics はもちろんのこと、structs, イベント, async/await, using/IDisposable, nullable context, attributes など経験があります。
  - 現在は C# の record を勉強しています。

### C/C++

- 大学で 3 年間ほど使い、ゲームやシステムユーティリティ、多種多様なプログラムを作ってきました。
- 現在は C++ を使い、Godot エンジンにコントリビューションしております。
- C は 3 年間、C++ は 2 年間、経験があります。
- 一人で作ったプログラム
  - C
    - libc の関数の一部
    - ls コマンド
    - select コマンド
    - shell（sh のクローン）
    - ソフトウェアレンダリングの疑似 3D 迷路探索ゲーム
    - メモリーアロケーター（malloc/free の代わりに使える関数）
    - nm/objdump（工夫点として、エラーハンドリングを容易にするために、C で使える例外ライブラリーを実装しました）
- グループで作ったプログラム
  - C
    - バイトコードを実行する VM とそのアセンブラー（アセンブラーの実装を担当）
    - レイトレーシングレンダラー（設定ファイルで使える Lisp をベースにした簡単なプログラミング言語を実装、アンチエイリアシングを実装）
    - 2D の仮想ワールドサーバーとその中でグループとして生存を図る AI（AI の実装を担当）
  - C++
    - SFML を使った 3D ボンバーマン（ゲームエンジンの基盤、ゲームロジック、マルチスレッドを実装）
    - ネットワーク上の音声通話プログラム（データシリアライザー、サーバーを実装）
    - ネットワーク上の 2D 横スクロールシューティングゲーム（ゲームエンジン、ゲームロジック、サーバーを実装）
- 細かいプログラムは省略しています。

### x86/x64 アセンブリー

- 大学で使ったことがあり、基礎的な知識があります。
- 一人で作ったプログラム
  - libc の関数の一部を再実装

## ゲームエンジン

- Unity 2021, 2022
  - 7 ヶ月の経験があります。
- Godot 4
  - 5 ヶ月の経験があります。

# 制作ゲーム

## トップダウンビューの 2D コロニービルダーゲーム

プレイヤーがエージェントに指示を出すことでコロニーを構築し、エージェントの生存を図るゲームです。  
エージェントはすべて AI で、プレイヤーが直接操作することができず、「構築」や「解体」の指示でのみ関節的に操作できます。  
エージェントには空腹があり、食べ続ける必要があります。胃袋が完全に空になると、エージェントはは死んでしまいます。

Oxygen Not Included からインスピレーションを受け、現在作ってるゲームです。  
C# でプログラミングしており、Unity バージョンと Godot バージョンがあります。

### ダウンロード

- Windows
- macOS

### 捜査方法

キーボード操作がなく、マウスの左クリックですべて捜査可能です。  
画面下部にあるボタンをクリックしてから、マップのどこかにクリックすると、エージェントに指示を出せます。  
手が空いたエージェントからその指示を実行します。

ボタンの意味は以下の通りです。

| ボタン                                                | 意味                                                 |
| ----------------------------------------------------- | ---------------------------------------------------- |
| ![steel_floor](assets/images/steel_floor.png)         | 鋼の地面を作る                                       |
| ![dirt_floor](assets/images/dirt_floor.png)           | 土の地面を作る                                       |
| ![potato_plant](assets/images/potato_plant.png)       | ジャガイモの植物を埋める<br>（土の地面でのみできる） |
| ![cancel_button](assets/images/cancel_button.png)     | 指示をキャンセルする                                 |
| ![demolish_button](assets/images/demolish_button.png) | 作られたものを解体する                               |

### ゲームプレイ動画

### ソースコード

もともと Unity で作っていたゲームですが、Godot に移行し、現在 Godot バージョンの方を開発しております。  
機能的には Unity バージョンと Godot バージョンは現在ほぼ一緒です。ちょうど Godot への移行が終わったところです。

- Unity バージョン
  - https://github.com/ershn/spaceship_game
- Godot バージョン
  - https://github.com/ershn/spaceship_game_godot

### 実装における工夫点

#### async/await を活かしたイベントベースの設計

#### シングルトンはなく、コンポーネントの再利用性が高い

#### ゲームエンティティの挙動はすべてエディター内で設定可能

#### カスタムツールの開発で実装コストを軽減

# Godot エンジンのコントリビューション

C++ とゲームエンジンの開発経験を身に着けるために、オープンソースのゲームエンジンである Godot にコントリビューションしております。
以下にコントリビューションしたプールリクエストの一部を掲載します。

## パスファインディングの修正

- [パスファインディングアルゴリズムの性能改善（アルゴリズムの時間計算を軽減）](https://github.com/godotengine/godot/pull/85965)
- [パスファインディングエージェントノードのバグ修正](https://github.com/godotengine/godot/pull/82561)

## コードリファクタリング

- [ソートの実装クラスのリファクタリングとユニットテスト追加](https://github.com/godotengine/godot/pull/85526)

## ドキュメンテーションの修正

- [コアクラスのドキュメンテーションの情報追加](https://github.com/godotengine/godot/pull/82889)
- [パスファインディングマニュアルの修正](https://github.com/godotengine/godot-docs/pull/8028)

## 改善提案

- [エディター内で実行されるユーザーの C# スクリプトのリロード方法の改善案](https://github.com/godotengine/godot-proposals/issues/9001)
