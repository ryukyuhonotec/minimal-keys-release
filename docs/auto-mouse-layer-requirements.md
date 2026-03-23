# Auto Mouse Layer 要件定義

## 背景

`minimal-keys` は右手側に `pmw3610` トラックボールを持ち、`MOUSE` レイヤーもすでに定義されています。
現状は `CONFIG_PMW3610_AUTOMOUSE_TIMEOUT_MS=700` により PMW3610 ドライバ標準のオートマウスレイヤーを使う前提ですが、記事「ZMKのオートマウスレイヤーを極める」（2025-06-15 公開）で紹介されている `Input Processor` ベースの方式へ移行すると、解除条件や誤爆防止を細かく制御できます。

参照記事:
https://zenn.dev/kot149/articles/zmk-auto-mouse-layer

## 現状整理

- トラックボールは右手側オーバーレイで `trackball` として定義済み。
- `trackball_listener` も右手側オーバーレイに定義済みだが、`input-processors` は未設定。
- `MOUSE` レイヤーは `minimal-keys.keymap` で `#define MOUSE 6` として定義済み。
- `MOUSE` レイヤーには右手ホーム段に `&mkp MB1 / MB3 / MB2` が配置済み。
- `CONFIG_PMW3610_AUTOMOUSE_TIMEOUT_MS=700` が有効で、現在は PMW3610 ドライバ側 AML が動いている前提。

## 目的

- トラックボールを動かしたとき、自動で `MOUSE` レイヤーを有効化する。
- キー入力とポインティングの切り替えを自然にし、常時マウスレイヤーを手動で保持しなくてよい状態にする。
- QMK 系 AML に近い使用感として、必要時のみマウスレイヤーへ入り、通常入力へ素早く戻れるようにする。

## スコープ

- `Input Processor` の `zip_temp_layer` を使った AML への移行。
- `trackball_listener` への `input-processors` 設定追加。
- PMW3610 ドライバ標準 AML と二重管理しないための無効化または実質停止。
- `MOUSE` レイヤー上のクリック系キー押下時に AML タイマーを延長する設定。
- 非マウスキー押下時に AML を解除するための `excluded-positions` 設定。
- 誤爆防止のための `require-prior-idle-ms` 導入可否の判断。

## スコープ外

- トラックボールの CPI や向き、加速度など AML 以外のポインタチューニング。
- `MOUSE` レイヤー自体の大規模再配置。
- Shift+クリック改善用の専用 hold-tap/macro 追加。
  これは必要になった時点で第2フェーズとして扱う。

## 要求仕様

### 1. 自動レイヤー起動

- トラックボール移動を契機に `MOUSE` レイヤーを有効化すること。
- 実装方式は PMW3610 ドライバ標準 AML ではなく `Input Processor` を優先すること。
- レイヤー番号は既存定義の `MOUSE=6` をそのまま使うこと。

### 2. レイヤー滞在時間

- 初回の AML タイムアウトは 700ms より長くすることを前提に調整する。
- 初期値候補は 2000ms から 10000ms の範囲で評価する。
- クリック後の再延長は通常の移動タイムアウトと分けて考えること。

### 3. 解除条件

- 非マウス用途のキーを押したら AML を解除できること。
- 解除対象から除外するキーは、少なくとも `MOUSE` レイヤー上のクリックキー位置を含めること。
- `excluded-positions` は現行の物理レイアウトに対して算出し、コメント付きで管理すること。

### 4. クリック時延長

- `&mkp` 押下時に AML タイマーを延長できること。
- これにより、移動後すぐクリックしてもレイヤーが落ちにくいこと。
- ダブルクリックしやすい値を選べるよう、延長時間は定数として見直しやすい形にすること。

### 5. 誤爆防止

- 直前にキー入力していた場合、意図しないボール接触で AML が発火しにくいこと。
- `require-prior-idle-ms` を使い、少なくとも 100ms から 250ms の範囲で評価できること。

### 6. 既存操作との整合

- 既存の `MOUSE` レイヤー配列は原則維持すること。
- 右手ホーム段のクリック配置は維持すること。
- ZMK Studio 利用や既存ビルド設定を壊さないこと。

## 受け入れ条件

- トラックボール移動で `MOUSE` レイヤー 6 が自動有効化される。
- `MOUSE` レイヤー上のクリックキーを押しても直ちに通常レイヤーへ戻らない。
- 通常キーを押すと AML が解除され、意図せずクリック入力にならない。
- 連続タイプ中に軽くボールへ触れただけでは AML が発火しにくい。
- PMW3610 ドライバ標準 AML と `Input Processor` AML が二重に競合しない。

## 実装対象ファイル

- `config/boards/shields/minimal-keys/minimal-keys_R.overlay`
- `config/boards/shields/minimal-keys/minimal-keys_R.conf`
- `config/minimal-keys.keymap`

## 未確定事項

- AML の基本タイムアウト値。
- `&mkp` 押下後の延長時間。
- `excluded-positions` にクリックキー以外の修飾キーを含めるか。
- `require-prior-idle-ms` を導入した場合の最適値。
- Shift+クリック改善を初回導入に含めるかどうか。
