# Fighters navi（Firebase リアルタイム共有版 / PWA対応）

デスクトップの `fighters-navi` フォルダ。

## 公開URL

**https://hamlocomoco1717-droid.github.io/fighters-navi/**

GitHub Pages でホスティング（`hamlocomoco1717-droid/fighters-navi` リポジトリの `main` ブランチ）。
`git push` すると GitHub Pages が自動で再ビルド（約1分）。ビルドコマンド不要の静的サイト。

- 旧URL `fighters-navi2026.netlify.app` … Netlify の無料クレジット枯渇でデプロイ停止。GitHub Pages へ移行済み。
- Firebase のバックエンドは共通なので、URLが変わってもデータはそのまま。

## デプロイの仕組み

必須ファイル（すべてリポジトリ直下）：
| ファイル | 役割 |
|---|---|
| `index.html` | アプリ本体 |
| `manifest.webmanifest` | PWA 設定（アプリ名・アイコン・起動画面） |
| `sw.js` | Service Worker（オフライン起動用） |
| `icon.svg` | アプリアイコン |
| `.nojekyll` | GitHub Pages が中身をそのまま配信するための空ファイル |

すべて相対パスなので、GitHub Pages のサブパス（`/fighters-navi/`）配信でもそのまま動作。

## スマホでホーム画面に追加

- **iPhone（Safari）**：共有ボタン → 「ホーム画面に追加」
- **Android（Chrome）**：⋮ → 「アプリをインストール」／「ホーム画面に追加」
- 追加後はアイコンから全画面で起動。電波が悪くてもアプリは開き、オンライン復帰時に同期。

## その他ファイル
| ファイル | 役割 |
|---|---|
| `firebase-setup.md` | Firebase セットアップ手順（初回のみ） |
| `database.rules.json` | Realtime Database のルール |
| `firebase.json` / `.firebaserc` | `firebase deploy` 用 |
| `import-*.json` | 取り込みサンプル／実データ |

## 右上バッジの意味
- **全員とリアルタイム同期中** … Firebase 接続OK
- **オフライン（再接続待ち）** … ネット未接続 or DBルール未設定
- **この端末のみ保存** … Firebase 設定値が読めていない
