---
title: 制作ゲーム
---

## トップダウンビューの 2D コロニービルダーゲーム

![gameplay_screenshot](assets/images/gameplay_screenshot.png)

プレイヤーがエージェントに指示を出すことでコロニーを構築し、エージェントの生存を図るゲームです。  
エージェントはすべて AI で、プレイヤーが直接操作することができず、「構築」や「解体」の指示でのみ関節的に操作できます。  
エージェントには空腹があり、食べ続ける必要があります。胃袋が完全に空になると、エージェントは倒れて、死んでしまいます。

Oxygen Not Included からインスピレーションを受け、現在作っているゲームです。  
C# でプログラミングしており、Unity バージョンと Godot バージョンがあります。  
完全に一人で制作しています。

### ダウンロード

- Windows
  - [Colony-builder-windows-x64.zip](https://github.com/ershn/spaceship_game_godot/releases/download/v0.1.0-alpha/Colony-builder-v0.1.0-alpha-windows-x64.zip)
  - ゲームを起動するには、`zip`ファイルを展開して、`exe`ファイルを実行してください
- macOS
  - TBA

### 捜査方法

マウスの左クリックですべて捜査可能です。  
画面下部にあるボタンをクリックしてから、マップのどこかをクリックすると、エージェントに指示を出せます。  
手が空いたエージェントからその指示を実行します。

ボタンの意味は以下の通りです。

| ボタン                                                | 意味                                                 |
| ----------------------------------------------------- | ---------------------------------------------------- |
| ![steel_floor](assets/images/steel_floor.png)         | 鋼のタイルを作る                                       |
| ![dirt_floor](assets/images/dirt_floor.png)           | 土のタイルを作る                                       |
| ![potato_plant](assets/images/potato_plant.png)       | ジャガイモを埋める<br>（土のタイルでのみできる） |
| ![cancel_button](assets/images/cancel_button.png)     | 指示をキャンセルする                                 |
| ![demolish_button](assets/images/demolish_button.png) | 作られたものを解体する                               |

以下の表で各アイテムを説明しています。

アイテム画像 | アイテム名称 | 用途
--- | --- | ---
![steel_item](assets/images/steel_item.png) | 鋼 | 鋼と土タイルの構築に必要
![dirt_item](assets/images/dirt_item.png) | 土 | 土タイルの構築に必要
![potato_item](assets/images/potato_item.png) | ジャガイモ | エージェントの食べ物
![potato_seed](assets/images/potato_seed.png) | ジャガイモの種 | ジャガイモを埋めるには必要
![nitratine_item](assets/images/nitratine_item.png) | 硝酸塩鉱物 | ジャガイモを育てるには必要

### ゲームプレイ動画

{% include video id="1-rUWlp-IoEAmF9ECJIKZtLpI6szZrkBo" provider="google-drive" %}

### ソースコード

もともと Unity で作っていたゲームですが、Godot に移行し、現在 Godot バージョンの方を開発しております。  
機能的には Unity バージョンと Godot バージョンは現在ほぼ一緒です。ちょうど Godot への移行が終わったところです。

- Unity バージョン
  - [https://github.com/ershn/spaceship_game](https://github.com/ershn/spaceship_game)
- Godot バージョン
  - [https://github.com/ershn/spaceship_game_godot](https://github.com/ershn/spaceship_game_godot)

### 実装における工夫点

#### async/await を活かした非同期な設計

必要な時だけ処理が走るようにしています。プーリングはほぼおらず、処理は基本的に非同期に行われます。  
最初はコールバックとイベントのみを使っていましたが、抽象化をしても分かりにくかったので、async/await や`TaskCompletionSource`を活かすようにしました。  
これにより、処理が非同期のままでも同期処理と同じ書き方になり、保守性があがりました。

以下は async/await を使って実装したエージェント用のジョブの一つです。

```csharp
public sealed class DeliverItemJob : IJob, IDisposable
{
    // ...

    public async Task Execute(PhysicsBody2D executor, CancellationToken ct)
    {
        var mover = executor.GetNode<Mover>("Mover");
        await mover.MoveTo(_item.GlobalPosition, ct);

        var backpack = executor.GetNode<Backpack>("Backpack");
        _item.Remove(_amount);
        backpack.Add(_item.Def, _amount);
        _item = null;

        try
        {
            await mover.MoveTo(_inventory.GlobalPosition, ct);
        }
        catch (TaskCanceledException)
        {
            backpack.Dump();
            throw;
        }

        backpack.Remove(_itemDef, _amount);
        _inventory.Add(_itemDef, _amount);
    }
}
```

関連クラス
- [IJob.cs](https://github.com/ershn/spaceship_game_godot/blob/9c67b4ee4648fe3a1e68f303c5adbfbe3a52a7a6/Scripts/Jobs/IJob.cs)　エージェントが実行するジョブのインターフェイス
- [DeliverItemJob.cs](https://github.com/ershn/spaceship_game_godot/blob/9c67b4ee4648fe3a1e68f303c5adbfbe3a52a7a6/Scripts/Jobs/DeliverItemJob.cs)　アイテムを特定のインベントリーに配達するジョブの実装
- [WorkOnJob.cs](https://github.com/ershn/spaceship_game_godot/blob/9c67b4ee4648fe3a1e68f303c5adbfbe3a52a7a6/Scripts/Jobs/WorkOnJob.cs)　エージェントの労力が必要な作業を対応するジョブの実装
- [JobScheduler.cs](https://github.com/ershn/spaceship_game_godot/blob/9c67b4ee4648fe3a1e68f303c5adbfbe3a52a7a6/Scripts/Jobs/JobScheduler.cs)　未処理ジョブをエージェントに配るスケジューラー
- [JobExecutor.cs](https://github.com/ershn/spaceship_game_godot/blob/9c67b4ee4648fe3a1e68f303c5adbfbe3a52a7a6/Scripts/Jobs/JobExecutor.cs)　エージェントにアタッチされてる、実際にジョブを実行するクラス

#### シングルトンはなく、各クラスの行数は抑えられてる

#### ゲームエンティティの挙動はすべてエディター内で設定可能

#### パスファインディングサブシステムを作成

#### カスタムツールの開発で実装コストを軽減
