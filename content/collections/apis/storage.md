---
title: サーバーにセーブを保存する
slug: storage
description: サーバーセーブ機能を利用するにはStorageAPIを利用してください。
order: 12
---

## 概要

 StorageAPIはサーバーセーブを可能にするAPIです。

### なにができるのか

機能を実行した際に、次のような操作を行います。

  - ゲーム内のセーブ情報をサーバーに保存する
  - サーバーに保存したセーブデータの削除を行う
  - サーバーに保存したセーブ情報を取得する

サーバーセーブに成功した場合、以下のようなポップアップが表示されます。  
![サーバーセーブ](/images/storage.png)

#### 利用想定／利用例
RPGツクールMVを含む `LocalStorage` を利用するゲームエンジンは、RPGアツマールが挿入する `rpgatsumaru.js` によって自動的にこの機能を利用しています。例えば以下のゲームがこの機能を利用してサーバーセーブを実行しています。
- [【防衛ゲーム】タタの魔法使い【異世界サバイバル】](https://game.nicovideo.jp/atsumaru/games/gm7601)
  - ゲーム内でセーブを実行した際に、`rpgatsumaru.js` が LocalStorage のデータを読み取り、自動的にサーバーへ保存しています。

## 詳細
Stringを保存するシンプルなKVS（Key-Valueストア)になっています。セーブはゲームID(gm123等)ごとに独立しています。
セーブデータの保存領域にはニコニコユーザーIDごとに上限があります。1kbで1ブロックとして、90ブロックまで保存ができます。セーブデータは保存時に自動的に圧縮され、圧縮後のファイルサイズを見ています。StorageAPIは複数端末からセーブされることを想定しています。

### 保存のkeyについて
- 推奨のkeyは「system」と「dataN」(Nは数字)です。この2種のキーはRPGアツマール側のセーブ管理画面で、それぞれ「システムファイル」と「ファイル N」として扱われます。この2つ以外はゲーム上で「その他」と表示されます。
- 「RPG Global」、「RPG Config」、「RPG FileN」(Nは数字)はRPGツクールMV用にカスタマイズされているため、RPGツクールMV以外ではセーブのkeyとしては使わないようにご注意ください。

## 利用方法

サーバーセーブ機能は次の方法で利用できます。


方法 | 場所
:---|:---
ゲームAPI | 以下の「APIでの利用方法」を参考に、直接APIを呼び出してください

### APIでの利用方法
APIを利用したサーバーセーブ機能の利用

### サーバーセーブ全件取得API
メソッド | window.RPGAtsumaru.storage.getItems()
:---|:---
説明 | サーバーに保存されているセーブデータを全件取得します。
引数 | なし
戻り値 | `Promise<{key: string, value: string}[]>`
リリース日 | 2016/12/27
更新日 | 2018/10/25

### サーバーセーブ保存API
メソッド | window.RPGAtsumaru.storage.setItems(items)
:---|:---
説明 | 引数に指定したitemsに対応するセーブデータを保存します。<br>複数端末からのセーブ時に楽観的ロックをしています。A端末とB端末で同時にゲームをプレイしていた場合にAとBがほぼ同時にsetItems()をした場合、先にsetItems()したもののみが受理されます。後のものは一度getItems()で最新のセーブデータを取得した後で受け付けとなります。<br>サーバーアクセスが発生します。
引数 | 更新する対象のセーブ情報。型は {key: string, value: string}[]
戻り値 | `Promise<void>`
リリース日 | 2016/12/27
更新日 | 2018/10/25

### サーバーセーブ削除API
メソッド | window.RPGAtsumaru.storage.removeItem(key)
:---|:---
説明 | 指定したセーブデータを削除します。<br>サーバーアクセスが発生します。
引数 | 削除対象のセーブのキー。型はstring
戻り値 | `Promise<void>`
リリース日 | 2016/12/27
更新日 | 2018/10/25
