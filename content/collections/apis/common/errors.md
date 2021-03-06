---
title: エラーハンドリング
slug: common/errors
description: RPGアツマールのゲームAPIが返すエラーについてのリファレンスです。
order: 4
---

## 概要
各RPGアツマール ゲームAPI共通で発生する可能性のある例外・エラーについての情報です。
基本的にプラグイン作者のかた向けの情報となります(プラグイン利用者には基本的には不要な情報となります)。
サーバーエラーや非ログイン等のエラーを扱いたいときに必要となる情報です。

## エラーオブジェクト

```ts
interface AtsumaruApiError {
  readonly errorType = "atsumaruApiError";
  readonly code: string;
  readonly message: string;
}
```

code一覧 | 解説
:---|:---
BAD_REQUEST | ゲーム側で何かしらAPIの使い方を間違えている場合のコードです
UNAUTHORIZED | プレイヤーが[ログイン](/common/login)している必要があるAPIを、プレイヤーが非ログイン状態で使った場合のコードです
API_CALL_LIMIT_EXCEEDED | [APIの呼び出し回数制限](/common/rate-limit) の上限に達した際に返されるコードです
FORBIDDEN | 次のように、プレイヤーやゲームがAPIに対してアクセス権限がない場合に発生します。<ul><li>ユーザーIDを指定するAPIで、対象のユーザー情報を取得する権限がない場合(相手のユーザーのプレイヤー通信有効化がされていないなど)</li><li>ゲームIDを指定するAPIで、対象のゲーム情報を取得する権限がない場合(対象のゲームが非公開であるなど)</li></ul>
INTERNAL_SERVER_ERROR | サーバー側で何らかの問題が発生していたり、通信に失敗した場合のコードです


### コード例

```js
window.RPGAtsumaru.experimental.scoreboards.setRecord(board_id, score)
  .catch(function(err) {
    switch(err.code) {
      case "BAD_REQUEST":
        // ゲーム側で何か間違えているとき＝指定したボードIDが大きすぎるかマイナスの場合などに発生
        /* エラーハンドリング処理 */
        break;
      case "INTERNAL_SERVER_ERROR":
        // サーバー側で何らかの問題＝通信不良やメンテ等で発生
        /* エラーハンドリング処理 */
        break;
    }
  })
```
