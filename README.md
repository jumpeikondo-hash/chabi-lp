# Chabi 公式LP

個人事業主のAIコンサル / AIセミナー事業「Chabi」の公式ランディングページ。

- **ドメイン**: [chabi.jp](https://chabi.jp/)
- **公開先**: Cloudflare Pages（GitHub 連携）
- **構成**: HTML5 + Tailwind CSS（CDN）+ 最小限のCSS
- **目的**: 信頼担保ハブ。SNS・チラシ・名刺からの流入先、および銀行・行政手続きの「事業実態確認」用途

---

## ファイル構成

```
chabi-lp/
├── index.html       # LP本体（1ファイルで完結）
├── README.md        # このファイル
└── .gitignore
```

ビルドツール・パッケージマネージャは未使用。`index.html` を直接ブラウザで開けば動きます。

---

## ローカルでの確認方法

```bash
# プロジェクトディレクトリへ
cd ~/Documents/chabi-lp

# ブラウザで開くだけ
open index.html
```

ライブリロードが欲しい場合（任意）:

```bash
# Python が入っていれば
python3 -m http.server 8000
# → http://localhost:8000 をブラウザで開く

# または Node が入っていれば
npx serve .
```

---

## デプロイ手順

### 1. GitHub にリポジトリを作成して push

```bash
cd ~/Documents/chabi-lp

# 初回コミット
git add .
git commit -m "Initial commit: Chabi LP MVP"

# GitHub 側で空のリポジトリ chabi-lp を作成しておく
# （Web UIから New repository → リポジトリ名: chabi-lp、Private/Publicはお好みで）

# リモート追加して push（your-username は自分のGitHubアカウント名に置き換え）
git branch -M main
git remote add origin git@github.com:your-username/chabi-lp.git
git push -u origin main
```

> SSH鍵を設定していない場合は `git@github.com:...` ではなく `https://github.com/your-username/chabi-lp.git` を使用。

### 2. Cloudflare Pages と連携

1. [Cloudflare Dashboard](https://dash.cloudflare.com/) にログイン
2. 左サイドバーから **Workers & Pages** → **Create application** → **Pages** タブ → **Connect to Git**
3. GitHub アカウントを認証し、`chabi-lp` リポジトリを選択
4. **ビルド設定**:
   - Framework preset: **None**
   - Build command: （空欄でOK）
   - Build output directory: `/`（または空欄）
   - Root directory: （空欄）
5. **Save and Deploy** をクリック
6. 数十秒で `https://chabi-lp.pages.dev` のような仮URLが発行される

以降、`main` ブランチに push するたびに自動デプロイされます。

### 3. 独自ドメイン chabi.jp を割り当てる

Cloudflare Pages 側:

1. デプロイされたプロジェクト → **Custom domains** タブ
2. **Set up a custom domain** → `chabi.jp` を入力
3. ルートドメインの場合は `www.chabi.jp` も併せて追加推奨
4. Cloudflare が自動でDNSレコードを提案してくれます

DNS 側（chabi.jp のネームサーバを Cloudflare に向けている前提）:

| Type  | Name | Content                       | Proxy  |
| ----- | ---- | ----------------------------- | ------ |
| CNAME | @    | chabi-lp.pages.dev            | Proxied |
| CNAME | www  | chabi-lp.pages.dev            | Proxied |

> ルートドメインで CNAME を切れるのは Cloudflare の **CNAME Flattening** のおかげ。他社DNSの場合は Cloudflare のIPに ALIAS / ANAME / A レコードで向ける必要あり。

ネームサーバが Cloudflare 以外の場合:

1. Cloudflare の指示に従って `chabi.jp` の **ネームサーバを Cloudflare に切り替え** ておく
2. ドメイン購入元（お名前.com など）の管理画面でネームサーバ変更
3. 浸透まで最大48時間（実際は数分〜数時間）

### 4. SSL / HTTPS

- Cloudflare Pages に Custom domain を追加すると、SSL証明書は自動発行される
- Cloudflare ダッシュボード → **SSL/TLS** → 暗号化モードは **Full** または **Full (strict)** 推奨

---

## 運用メモ

### コンテンツ更新の流れ

```bash
# index.html を編集後
git add index.html
git commit -m "Update: ヒーローのコピー修正"
git push
# → Cloudflare Pages が自動デプロイ（1〜2分）
```

### 後日差し替え予定

- [ ] `og:image`（現在は仮URL `https://chabi.jp/og-image.png`）
- [ ] ケーススタディ詳細ページ（5月末公開予定 → `/cases/recruiting-usa` などのパス・媒体名は伏せ字運用）
- [ ] 代表者の実名公開（育休明けタイミングで）
- [ ] favicon を SVG モノグラムから正規ロゴに

### ブランドトークン

`index.html` 内の Tailwind config と `:root` CSS変数で管理:

| 用途       | カラー    | 変数名               |
| ---------- | --------- | -------------------- |
| 背景       | `#f1ebdd` | `--color-base`       |
| 本文       | `#0f0f0f` | `--color-text`       |
| アクセント | `#1d88b8` | `--color-accent`     |
| 補足       | `#6b6b6b` | `--color-muted`      |
| カード背景 | `#ffffff` | `--color-white`      |

### フォント

- 見出し・本文: **Noto Sans JP**（400/700/900）
- 数字強調: **Inter**（600/700/800）

いずれも Google Fonts 経由で読み込み。

---

## 連絡先

- メール: [jumpei_kondo@chabi.jp](mailto:jumpei_kondo@chabi.jp)

---

© 2026 Chabi
