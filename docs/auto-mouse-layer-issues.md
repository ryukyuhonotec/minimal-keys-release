# Auto Mouse Layer 実装イシュー

## Issue 1: 現状 AML を Input Processor 方式へ切り替える

### 目的

PMW3610 ドライバ標準 AML から `zip_temp_layer` ベースへ移行し、AML の挙動を ZMK 側で柔軟に制御できるようにする。

### 対応内容

- `minimal-keys_R.overlay` に `#include <input/processors.dtsi>` を追加する。
- `trackball_listener` に `input-processors = <&zip_temp_layer MOUSE timeout>;` を設定する。
- PMW3610 ドライバ標準 AML を無効化する。
  具体的には `minimal-keys_R.conf` の `CONFIG_PMW3610_AUTOMOUSE_TIMEOUT_MS` を削除または無効値へ変更する。

### 完了条件

- トラックボール移動で `MOUSE` レイヤーが有効になる。
- AML の制御元が `Input Processor` に一本化されている。

## Issue 2: 非マウスキー押下時の AML 解除条件を定義する

### 目的

通常入力へ戻りたい時に、マウスレイヤーが残って誤クリックする状態を防ぐ。

### 対応内容

- 物理レイアウト上の position を洗い出す。
- `MOUSE` レイヤー上の `MB1 / MB3 / MB2` の position を `excluded-positions` に追加する。
- 必要なら Shift などクリック補助に使うキーを候補として追加し、初回導入範囲かを判断する。

### 完了条件

- クリック系キー押下では AML が即解除されない。
- それ以外の通常キー押下で AML が解除される。

## Issue 3: `&mkp` 押下で AML タイマーを延長する

### 目的

移動後のクリック、ダブルクリック、ドラッグ開始を安定させる。

### 対応内容

- `minimal-keys.keymap` に `#include <input/processors.dtsi>` を追加する。
- `&mkp_input_listener` に `input-processors = <&zip_temp_layer MOUSE click_timeout>;` を設定する。
- 初期値として 250ms, 700ms, 1000ms などを比較し、実機で評価しやすい値にする。

### 完了条件

- クリック時に AML が延長される。
- クリック直後にレイヤーが落ちて操作が途切れない。

## Issue 4: 誤爆防止のため `require-prior-idle-ms` を導入する

### 目的

タイピング中に軽くトラックボールへ触れただけで AML が発火する問題を抑える。

### 対応内容

- `&zip_temp_layer` に `require-prior-idle-ms` を設定する。
- 100ms, 150ms, 200ms, 250ms を候補に評価する。
- タイピング時の誤発火と、狙ってすぐマウスへ移りたい時の反応速度のバランスを確認する。

### 完了条件

- タイピング中の誤発火が許容範囲に収まる。
- 反応が鈍すぎて実運用でストレスにならない。

## Issue 5: パラメータ調整と動作確認

### 目的

AML の体感品質を、実際の運用に耐える値まで詰める。

### 対応内容

- 基本タイムアウトを調整する。
- クリック延長時間を調整する。
- `excluded-positions` の対象キーを見直す。
- 次の観点で確認する。
  - 移動してからクリック
  - ダブルクリック
  - ドラッグ開始
  - タイピング直後の誤発火
  - 通常キー押下でのレイヤー解除

### 完了条件

- 普段使いで誤爆や取りこぼしが目立たない。
- 次に再調整する際、どの値を触ればよいか分かる状態で残っている。

## 保留 Issue: Shift+クリック改善

### 背景

記事では、Mod-Tap の Shift を押した時だけ AML を維持し、タップ時のみ解除するための専用 macro/behavior 構成が紹介されている。

### 今回の扱い

- 初回導入には含めない。
- テキスト選択などで実害が出たら別 Issue として切り出す。

## 推奨実装順

1. Issue 1
2. Issue 2
3. Issue 3
4. Issue 4
5. Issue 5
