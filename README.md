# Lounge亜子 シフトアプリ — 引き継ぎ用README（Claude Code）

お店「Lounge亜子」のスマホ用シフト管理アプリ。**単一ファイル `index.html`**（素のHTML/CSS/JS）に全機能を内包。データ同期は Firebase Realtime Database、ホスティングは **GitHub Pages 自動デプロイ**。

- 本番URL: https://stylesince2000-hash.github.io/lounge-ako-shift/
- リポジトリ（Public）: https://github.com/stylesince2000-hash/lounge-ako-shift

> この README は公開リポジトリに入るため、パスワード等の秘密は書きません。詳細な運用メモ（パスワード一覧など）は、各自のPCローカルにある `CLAUDE.md`（gitignore・非公開）を参照。無ければ元の担当者から受け取ってください。

## 他PCで続きを始める手順
```bash
gh auth login          # 初回のみ：GitHubにログイン
git clone https://github.com/stylesince2000-hash/lounge-ako-shift
cd lounge-ako-shift
claude                 # このフォルダで Claude Code を起動し、まず本READMEを読ませる
```
- **作業前**に必ず `git pull`、**作業後**は下記手順で `git push`。
- シフトの入力データは Firebase 上にあるので、どのPCで編集しても本番データは共通。

## ファイル構成
| ファイル | 役割 | Git |
|---|---|---|
| `index.html` | 本体（唯一の実装ファイル） | ✅ 追跡 |
| `logo-dark.png` / `logo-light.png` | ログイン画面／上部バーのロゴ | ✅ 追跡 |
| `icon.png` | ホーム画面追加時のアプリアイコン（512×512） | ✅ 追跡 |
| `manifest.json` | PWAマニフェスト（**Androidのホーム画面アイコンに必須**） | ✅ 追跡 |
| `version.json` | 自動アップデート用の版番号 | ✅ 追跡 |
| `.nojekyll` | GitHub PagesのJekyll処理を無効化（**無いとビルド失敗し画像等が404**） | ✅ 追跡 |
| `CLAUDE.md` / `セットアップ手順.md` | 運用メモ・利用者マニュアル（パスワード記載） | 🚫 非公開(gitignore) |

## デプロイ方法（GitHub Pages 自動）
1. `index.html`（必要ならロゴ等）を編集。
2. **★重要**: 動作に影響する変更をしたら、`index.html` の `const APP_VERSION` と `version.json` の `version` を**同じ新しい値**へ上げる（例 `2026.07.04-2`）。開きっぱなし端末の自動リロード用。両者は必ず一致させる。
3. `git add -A && git commit -m "変更内容" && git push`
4. 1〜2分待つ → 本番URLに自動反映。ビルド状況: `gh api repos/stylesince2000-hash/lounge-ako-shift/pages --jq .status`（`built`で完了）。

## 端末まわりの挙動
- **ズーム禁止**: ピンチ・同一箇所ダブルタップを抑止（別セルの素早い連打は誤爆しない）。
- **自動アップデート**: `version.json` と `APP_VERSION` を比較し、タブ復帰・フォーカス・bfcache復帰・3分間隔で確認、違えば自動リロード（二重リロード防止ガードあり）。
- **操作音**: Web Audioで合成（音声ファイル不要）。上部バーの 🔊/🔇 でON/OFF（端末ごと・既定ON）。

## 技術メモ（要点）
- フレームワーク不使用。`<script>` 内に全ロジック。状態は `store` オブジェクト（staff, avail, schedule, closures, opens, events, dayNotes, notice, loginLog, settings）。
- 保存: localStorage（キー `cafeShift_v1`）＋ Firebase（`rooms/lounge-ako-main`）。`persist()` が両方へ書き、Firebaseの `on('value')` で全端末同期。
- Firebase設定は `index.html` 冒頭の `FIREBASE_CONFIG`（クライアント公開前提の値）。
- パスワード（従業員／管理者／開発者）は `index.html` 内の初期値とアプリ内「⚙️設定」で管理。具体値は本READMEには記載しない。
