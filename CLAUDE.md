# CLAUDE.md

このリポジトリで作業する際のガイドです。

## プロジェクト概要

HTMLだけで動くWBS（Work Breakdown Structure）/ ガントチャートツール。
`index.html` をブラウザで開くだけで動作する。サーバー・ビルド・パッケージ管理・依存ライブラリは一切なし。

- 主要ファイル: [`index.html`](index.html) — HTML/CSS/JS をすべて内包した単一ファイル
- データ永続化: ブラウザの `localStorage`（キー: `wbs-tool-data-v1`）。外部送信は行わない。

## 構成

すべて `index.html` 一枚に収まっている。

- `<style>` — 全UIのCSS（CSS変数でテーマ管理）
- 左ペイン: WBSテーブル（階層タスク、WBS番号 1.2.3 を自動採番）
- 右ペイン: ガントチャート（`DAY_W` px/日でバー描画、土日シェーディング・今日の線・マイルストーン・親のサマリーバー）
- `<script>` — 状態は `tasks[]`（`{id, level, name, assignee, start, end, progress, status, collapsed}`）の単一配列。階層は `level` で表現し、ある行の子は「直後から `level` が大きい行が続く範囲」として扱う。

### 主要な関数

- `render()` … `renderTable()` + `renderGantt()` + `renderStats()` + `save()`
- `childrenRange(idx)` … 子タスクの範囲 `[start, endExclusive)` を返す（階層操作の基礎）
- `summaryOf(idx)` … 親タスクの開始/終了/進捗を葉ノードから集計（ロールアップ）
- `wbsNumber(idx)` … 階層番号を採番
- 操作系: `addRoot/addChild/addSibling/deleteTask/indent/outdent/moveUp/moveDown`
- 入出力: `exportJSON/exportCSV/importJSON`、`loadSample`

## 動作確認

ビルド不要。`index.html` をブラウザで直接開くか、任意の静的サーバーで配信する。

```
npx --yes http-server -p 8777 -c-1
```

（ローカルプレビュー設定 `.claude/launch.json` は `.gitignore` で除外済み。`python` はこの環境ではWindowsストアのスタブで動かないため Node を使う。）

## 開発ワークフロー（必須ルール）

以下は**必ず守る**こと:

1. **作業は機能単位で Issue を立ててから始める。** 実装の前に GitHub Issue を作成し、何を作るか・受け入れ条件を明記する。
2. **作業はブランチを切って行い、PR を出す。** 直接 `main` にコミットしない。PR 本文に `Closes #<issue番号>` を入れて Issue と紐付ける。
3. **PR を出した後、必ず `/code-review` を実施する。** 指摘事項はマージ前に対応するか、対応しない場合は PR コメント or 別 Issue として残す。

### 補足

- コミット・プッシュ・PR作成・マージは、ユーザーが明示的に指示したときのみ行う。
- コミットメッセージ・PR・Issue は日本語で記述する。
- 単一ファイル構成を崩さない（外部依存・ビルド工程を持ち込まない）ことを基本方針とする。
