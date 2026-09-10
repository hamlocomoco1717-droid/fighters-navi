# Fighters navi ― Firebase リアルタイム共有 セットアップ手順

`index.html` を、**リンクを知っている人なら誰でも同じスコアをリアルタイム編集できる**
ようにするための設定です。所要 10〜15 分。無料枠（Spark プラン）で十分動きます。

---

## 仕組み（ざっくり）

- スコアブック全体を Firebase Realtime Database の 1 か所（`scorebook/fighters`）に保存
- 誰かが入力すると 0.4 秒後に自動保存 → 他の全員の画面に即反映
- 同時に自分のブラウザにも保存するので、オフラインでも閲覧・入力でき、再接続時に同期
- 競合は「最後に保存した人が勝ち」。入力中は他人の変更で画面が飛ばないよう保留してから反映

---

## 1. Firebase プロジェクトを作る

1. <https://console.firebase.google.com/> に Google アカウントでログイン
2. **「プロジェクトを追加」** → 名前（例: `fighters-navi`）→ 作成
   - Google アナリティクスは「無効」でOK

## 2. Realtime Database を作る

1. 左メニュー **「構築」→「Realtime Database」** → **「データベースを作成」**
2. ロケーション: `United States (us-central1)` などそのままでOK
3. セキュリティルール: **「テストモードで開始」** を選択（あとで下記ルールに差し替え）
4. 作成後、画面上部に出る URL を控える
   （例: `https://fighters-navi-default-rtdb.firebaseio.com`）

## 3. Web アプリを登録して設定値を取得

1. 左上 **⚙ →「プロジェクトの設定」** →「全般」タブ
2. 「マイアプリ」で **`</>`（ウェブ）** アイコンをクリック
3. アプリ名（例: `web`）を入力 →「アプリを登録」（Hosting のチェックは任意）
4. 表示される **`firebaseConfig`** の中身をコピー

## 4. HTML に設定を貼る

`index.html` を開き、上のほうにある
`<script id="firebase-config">` ブロックの `PASTE_...` を、手順 3 の値で置き換えます。

```js
window.FIREBASE_CONFIG = {
  apiKey: "AIza........",
  authDomain: "fighters-navi.firebaseapp.com",
  databaseURL: "https://fighters-navi-default-rtdb.firebaseio.com",  // 手順2のURL
  projectId: "fighters-navi",
  storageBucket: "fighters-navi.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcdef......"
};
window.FIREBASE_PATH = "scorebook/fighters";  // 変えなくてOK
```

> `databaseURL` が config に含まれていないことがあります。その場合は手順 2 で控えた URL を
> 手入力してください（これが無いと同期しません）。

## 5. セキュリティルールを設定

Realtime Database →「ルール」タブに、同梱の `database.rules.json` の内容を貼って「公開」。

```json
{
  "rules": {
    "scorebook": {
      ".read": true,
      ".write": true
    }
  }
}
```

- これは「URL を知っていれば誰でも読み書き可」の設定です（今回のご要望どおり）。
- 気になる場合の緩い保護案は最後の「補足」を参照。

## 6. 公開する（どれか1つ）

### A. Firebase Hosting（推奨・`firebase deploy` 一発）

```bash
npm install -g firebase-tools
firebase login
# このフォルダで:
firebase init hosting      # 既存プロジェクトを選択 / public ディレクトリは "." / SPA: No / 上書き: No
firebase deploy
```

`.firebaserc` / `firebase.json` の雛形を同梱しています。`.firebaserc` の
`YOUR_PROJECT_ID` を実際のプロジェクト ID に変えれば `firebase init` は省略可。
デプロイ後に出る `https://<project>.web.app/` を共有。

### B. 他の静的ホスティングでもOK

Netlify Drop / GitHub Pages / Cloudflare Pages などに `index.html` を
置くだけ。Firebase 側は手順 5 まででOK（Hosting は不要）。

### C. とりあえず動作確認だけ

ファイルをダブルクリックしてブラウザで開いても Firebase 同期は動きます
（`file://` でも可）。複数端末で試すときは A か B で。

---

## 動作確認

1. 公開 URL を 2 つの端末（またはPCとスマホ）で開く
2. 右上のバッジが **「全員とリアルタイム同期中」** になっていることを確認
   - 「この端末のみ保存」= 設定値が未反映。手順 4 を見直す
   - 「オフライン（再接続待ち）」= ネットワーク or `databaseURL` 誤り
3. 片方でスコアを入力 → もう片方に 1 秒以内で反映されればOK
4. うまくいかないときはブラウザの開発者ツール → Console の `[FBSync]` ログを確認

---

## 既存データについて

同梱の HTML には現在のファイターズのデータ（選手一覧・全試合）が埋め込み済みです。
**サーバーが空の初回起動時に、そのデータが共有データとして自動アップロード**されます。
2 回目以降はサーバー側が正になります。

やり直したいとき: Realtime Database の `scorebook` ノードを画面上で削除 → 誰かが再度開くと再アップロード。

---

## 補足：もう少しだけ保護したい場合

「URL 流出で誰でも書ける」が不安なら、いちばん軽い対策は
**Firebase Authentication の「匿名ログイン」を有効化**して、ルールを次のようにする方法です。

```json
{
  "rules": {
    "scorebook": {
      ".read": "auth != null",
      ".write": "auth != null"
    }
  }
}
```

この場合 HTML 側にも匿名サインイン処理の追加が必要です（`firebase-auth-compat.js` の読み込みと
`firebase.auth().signInAnonymously()`）。必要ならこの改修も対応します。

App Check（reCAPTCHA）を足すとボット書き込みをさらに抑えられます。
