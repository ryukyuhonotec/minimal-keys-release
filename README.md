# minimal-keys

`minimal-keys` は ZMK ベースの格子配列キーボード設定です。
このリポジトリには、キーマップ、シールド定義、右手側トラックボールの設定を含みます。

## 構成

- `config/minimal-keys.keymap`
  レイヤー定義と主要な behavior 設定。
- `config/boards/shields/minimal-keys/minimal-keys.dtsi`
  物理レイアウトと matrix transform 定義。
- `config/boards/shields/minimal-keys/minimal-keys_L.overlay`
  左手側の GPIO, encoder 設定。
- `config/boards/shields/minimal-keys/minimal-keys_R.overlay`
  右手側の GPIO, SPI, PMW3610 トラックボール設定。
- `config/boards/shields/minimal-keys/minimal-keys_L.conf`
  左手側のビルド設定。
- `config/boards/shields/minimal-keys/minimal-keys_R.conf`
  右手側のビルド設定。
- `config/minimal-keys.json`
  レイアウト JSON。

## 現在の入力デバイス構成

- 右手側に `pmw3610` トラックボールを搭載。
- `MOUSE` レイヤーは `config/minimal-keys.keymap` で定義済み。
- オートマウスレイヤーは `Input Processor` ベースで設定済み。

## Auto Mouse Layer 関連ドキュメント

- `docs/auto-mouse-layer-requirements.md`
  オートマウスレイヤー導入の要件定義。
- `docs/auto-mouse-layer-issues.md`
  実装順に分けたイシュー一覧。
- `docs/auto-mouse-layer-tuning.md`
  現在の初期値と実機調整メモ。

参照記事:
https://zenn.dev/kot149/articles/zmk-auto-mouse-layer

## メモ

- 現状の `MOUSE` レイヤー番号は `6`。
- トラックボール設定の主な編集先は右手側の `.overlay` と `.conf`。
- AML 実装時は PMW3610 ドライバ標準 AML と `Input Processor` AML の二重有効化を避ける。
