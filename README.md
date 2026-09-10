# Fighters navi（Firebase リアルタイム共有版 / PWA対応）

デスクトップの `fighters-navi` フォルダ。

## 公開（デプロイ）— ここが変わりました

PWA化（ホーム画面アイコン・オフライン起動）に伴い、**`index.html` 単体ではなくフォルダごと**デプロイしてください。

**必須ファイル（サイト直下に置く）**
| ファイル | 役割 |
|---|---|
| `index.html` | アプリ本体 |
| `manifest.webmanifest` | PWA 設定（アプリ名・アイコン・起動画面） |
| `sw.js` | Service Worker（オフライン起動用。**サイト直下**必須） |
| `icon.svg` | アプリアイコン |

**Netlify Drop の場合**：`fighters-navi` フォルダを https://app.netlify.com/drop（または既存サイトの Deploys → ドラッグ）に**フォルダごと**ドロップ。URL（`fighters-navi2026.netlify.app`）は変わりません。
→ `README.md` や `*.json` サンプルも一緒に公開されますが実害はありません（消したい場合は必須4ファイルだけ別フォルダにまとめてドロップ）。

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
