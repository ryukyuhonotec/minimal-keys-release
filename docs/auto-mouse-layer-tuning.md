# Auto Mouse Layer 調整メモ

## 現在の初期値

- トラックボール移動による AML 有効時間: `3000ms`
- `&mkp` 押下時の AML 延長時間: `700ms`
- 誤爆防止の `require-prior-idle-ms`: `150ms`
- クリック用 `excluded-positions`: `18`, `19`, `20`

## 主な編集箇所

- `config/boards/shields/minimal-keys/minimal-keys_R.overlay`
  - `trackball_listener` の `input-processors = <&zip_temp_layer 6 3000>;`
  - `&zip_temp_layer` の `require-prior-idle-ms`
  - `&zip_temp_layer` の `excluded-positions`
- `config/minimal-keys.keymap`
  - `&mkp_input_listener` の `input-processors = <&zip_temp_layer MOUSE 700>;`

## 調整の目安

- AML がすぐ切れる:
  - `3000ms` を `5000ms` へ上げる。
- 通常入力へ戻るのが遅い:
  - `3000ms` を `2000ms` へ下げる。
- ダブルクリックしづらい:
  - `700ms` を `900ms` から `1000ms` へ上げる。
- クリック後にレイヤーが残りすぎる:
  - `700ms` を `500ms` 前後へ下げる。
- タイピング中に誤発火する:
  - `150ms` を `200ms` または `250ms` へ上げる。
- マウスへ移りたい時の反応が鈍い:
  - `150ms` を `100ms` へ下げる。

## 手動確認チェックリスト

1. タイピング直後に軽くボールへ触れ、誤発火しないか確認する。
2. ボールを動かしてから通常の単クリックが安定するか確認する。
3. ダブルクリックが自然なテンポで入るか確認する。
4. ドラッグ開始時にレイヤーが途中で落ちないか確認する。
5. AML 中に通常キーを押したとき、即座に通常入力へ戻るか確認する。
6. クリックキー `MB1 / MB3 / MB2` では AML が維持されるか確認する。

## 次に見直す候補

- Shift を使ったテキスト選択で不都合が出るなら、Shift+クリック改善を別 Issue として追加する。
- `excluded-positions` に修飾キーを足すかは、実機での選択操作を見て判断する。
