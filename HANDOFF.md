# Chabi LP — 引き継ぎ書

> 開発を別環境（Antigravity 等）に引き継ぐ際の単独キャッチアップ用ドキュメント。  
> プロジェクト状態・運用ルール・意思決定の経緯まで網羅。

最終更新: 2026-05-XX  
直近の作業者: Claude Code セッション（このコミットまで）

---

## 1. プロジェクト概要

**Chabi**（チャビ）は個人事業主の AI コンサルティング / AI セミナー事業。本リポジトリはその公式ランディングページ。

| 項目 | 値 |
|---|---|
| ドメイン | [chabi.jp](https://chabi.jp/) |
| ホスティング | Cloudflare Pages（GitHub 連携） |
| GitHub | [jumpeikondo-hash/chabi-lp](https://github.com/jumpeikondo-hash/chabi-lp) |
| 可視性 | Public |
| メインブランチ | `main`（push で自動デプロイ） |
| ローカルパス | `~/Documents/chabi-lp/` |
| 主な目的 | 信頼担保ハブ（SNS / 名刺 / 銀行・行政の事業実態確認材料） |

---

## 2. 技術スタック

- **HTML5**: シングルファイル `index.html` で完結
- **CSS**: Tailwind CSS（**CDN 経由**・ビルド不要） + `<style>` 内のカスタム CSS
- **JS**: Tally embed（外部スクリプト）と CSS `scroll-behavior: smooth` のみ
- **画像**: `assets/` 配下に静的配置
- **Cloudflare**: `wrangler.jsonc` で静的 assets 配信設定

### なぜビルドツール無しか
**MVP 速度優先**。今日中にデプロイして銀行・取引先に提示する必要があったため。今後フレームワーク化する場合は Astro / Next.js への移行が現実的。

---

## 3. ファイル構成

```
chabi-lp/
├── index.html              # LP 本体（編集の中心）
├── README.md               # 一般的なデプロイ手順
├── HANDOFF.md              # この文書
├── .gitignore
├── wrangler.jsonc          # Cloudflare Workers/Pages config（基本触らない）
└── assets/
    ├── hero.jpg            # ヒーロー画像（Unsplash by Nick Morrison）
    ├── jp_monogram_icon.svg  # ヘッダー/フッターの「△＋青バー」モノグラム
    └── jp_profile_icon.svg   # プロフ用（og:image 等で将来使用予定）
```

---

## 4. ブランドトークン

### カラー（`index.html` の Tailwind config + `<style>` 内の CSS 変数）

| 用途 | カラー | Tailwind | CSS 変数 |
|---|---|---|---|
| 背景（メイン） | `#f1ebdd` | `bg-base` | `--color-base` |
| 本文テキスト | `#0f0f0f` | `text-ink` | `--color-text` |
| アクセント・CTA | `#1d88b8` | `bg-accent` / `text-accent` | `--color-accent` |
| アクセント濃 | `#176f96` | `bg-accent-dark` | — |
| カード背景 | `#ffffff` | `bg-white` | `--color-white` |
| 補足テキスト | `#6b6b6b` | `text-muted` | `--color-muted` |
| 罫線（薄ベージュ） | `#e6dfce` | `border-line` | — |

### フォント（Google Fonts）
- **本文・見出し**: Noto Sans JP（400 / 500 / 700 / 900）
- **数字・欧文強調**: Inter（600 / 700 / 800）

### 重要な CSS ルール
```css
/* 日本語の文節・カタカナ語の途中改行を抑制（日本語LP定番） */
h1, h2, h3, p {
  word-break: keep-all;
  line-break: strict;
  overflow-wrap: break-word;
}
.heading { font-weight: 900; letter-spacing: -0.01em; }
.num     { font-family: 'Inter', sans-serif; font-feature-settings: 'tnum'; }
```

### タイポグラフィのコツ（実体験から）
- **重要なラベルは `font-bold`(700) ではなく `font-black`(900) を使う**  
  Noto Sans JP の 700 は macOS subpixel rendering でやや細く見えがち。900 のほうが視認性が安定。
- **数字と単位の間に半角スペースを入れる**（例: `150 分`, `+6 件/週`）  
  日本語タイポグラフィの可読性向上
- **末尾の「。」がぶら下がる場合**は `<span class="whitespace-nowrap">事例。</span>` で文字とくくる

---

## 5. ページ構成（6セクション + Footer）

| 順 | ID | 背景色 | 内容 |
|---|---|---|---|
| 1 | （Sticky Header） | `bg-base/80 backdrop-blur` | ロゴ + ナビ + CTA |
| 2 | `#top` Hero | `bg-base`（ベージュ） | **2カラム**: 左テキスト・右 `hero.jpg`（sepia フィルター適用） |
| 3 | `#service` Service | `bg-white` | 2カード（AIセミナー / AIコンサルティング） |
| 4 | `#results` Results | `bg-base` | 数字3カード + APPROACH 3カード + 補足注釈 |
| 5 | `#about` About | **`bg-accent`（青）** | 代表者のスラッシュbio。**白文字でアクセント区間** |
| 6 | `#contact` Contact | `bg-base` | 大CTA + リード文 + 補足 |
| 7 | （Footer） | `#e6dfce`（薄ベージュ） | モノグラム + 事業形態 + ドメイン + © |

---

## 6. 重要な運用ルール（**絶対遵守**）

### A. 代表者の個人名は公開しない
- 育休中のため非公開
- bio は **スラッシュ表記の経歴のみ**（資格・職歴）
- JSON-LD も Organization 単位（Person スキーマは使わない）

### B. クライアント / 媒体名は匿名化
公開資料（LP・proposal deck・case study すべて）で統一：

| 実名 | 公開表記 |
|---|---|
| Akri USA / Akri | **米国の人材紹介事業者** |
| Indeed スカウト | **スカウト媒体A** |
| Indeed 求人 | **求人媒体B** |
| びびなび | **求人媒体C** |

### C. 業務名の統一
- 「採用業務」「面談業務」→ **「候補者対応業務」** に統一

### D. ROI 表記は誤読されない書き方
✅ `売上機会 $117,700 ÷ 投資 $4,272 = 27.5倍/年`  
❌ `ROI 約27倍`（説明不足）

### E. コミットメッセージも匿名化原則を踏襲
Public リポジトリの commit ログは外部から見える。実名（Akri / Indeed / びびなび）は commit message にも書かない。

---

## 7. 主要な数字（**Akri 案件・確定版 v5 / 2026-05-13 確定**）

| 指標 | 値 | LP 内ラベル |
|---|---|---|
| 1面談あたり作業時間削減 | **-40%**（150分 → 90分） | TIME SAVED |
| 面談キャパ拡張 | 週 **10 → 16件**（+60%）| CAPACITY |
| 年間 ROI | **27.5倍**（売上機会 $117,700 ÷ 投資 $4,272）| ANNUAL ROI |

詳細は別ページ `/cases/recruiting-usa`（5月末公開予定・未着手）。  
**旧数字（-67% / 22.5h→7.5h / +$120K / ROI 約2,000倍）は廃棄済**。

---

## 8. CTA / フォーム

**全 CTA は Tally Popup**（form id: `pbJr8b`）に統一。mailto: は廃止済。

```html
<button data-tally-open="pbJr8b" data-tally-layout="modal"
        data-tally-width="700" data-tally-emoji-text="👋"
        data-tally-emoji-animation="wave"
        class="inline-flex items-center justify-center min-h-[52px] px-7
               rounded-md bg-accent text-white font-bold
               hover:bg-accent-dark transition shadow-card">
  お問い合わせ
</button>
```

- 文言は **「お問い合わせ」で統一**（「メールで問い合わせる」「無料診断を予約する」は廃止）
- Tally の embed script は `<head>` 末尾でロード:  
  `<script async src="https://tally.so/widgets/embed.js"></script>`

---

## 9. ローカル開発

### dev サーバー起動
```bash
cd ~/Documents/chabi-lp
npx serve . -l 3000
# → http://localhost:3000
```

### Cloudflare 本番相当の検証
```bash
npx wrangler dev
# → http://localhost:8787 で Cloudflare Workers ランタイムで確認
```

### ⚠️ 既知の制約：Python の `http.server` は使えない
このマシンの harness CWD が macOS の Google Drive CloudStorage 配下にあり、Python が起動時の `os.getcwd()` で PermissionError を出す。**Node ベースの `npx serve` または `npx wrangler dev` を使うこと**。

---

## 10. デプロイ

```bash
git add <files>
git commit -m "<message>"
git push origin main
# → Cloudflare Pages が自動デプロイ（1〜2 分後に chabi.jp に反映）
```

### Cloudflare Pages 設定（参考）
- Framework preset: **None**
- Build command: なし
- Build output directory: `/`
- Custom domain: `chabi.jp`（DNS で `@` と `www` を `chabi-lp.pages.dev` に CNAME・Cloudflare proxied）

---

## 11. ペンディング / 今後のタスク

### 短期（次に着手）
- [ ] **ケーススタディ詳細ページ** `/cases/recruiting-usa`（5月末予定・**最優先**）  
  → v5 詳細コンテンツ（4.5ヶ月実績 / 業務時間構造 / 3シナリオ比較 / ROI内訳 / 戦略の核）を1ページに
- [ ] `og:image` の実画像を作成（現在は仮 URL `https://chabi.jp/og-image.png`）
- [ ] favicon を SVG モノグラムから正規ロゴに置き換え（必要であれば）

### 中期
- [ ] 育休明けに代表者の実名・写真公開を検討
- [ ] Service カードに料金レンジ追加（クライアント反応見て判断）
- [ ] 事例（Case Study）が増えてきたら一覧ページ化（`/cases/`）

### 長期検討
- シングル `index.html` が肥大化したら Astro / Next.js への移行
- 多言語対応（英語）の必要性が出たら i18n 構造を検討

---

## 12. 直近のデザイン決定の経緯（**手戻り防止用**）

時系列で「なぜそうなったか」を記録。

1. **About の強み三角3カードを削除**: Results の APPROACH と内容が重複していたため
2. **APPROACH を罫線レイアウト → 白カードに復活**: 罫線案は試したが、Results 内カードとの統一感が崩れたため白カードに復元
3. **APPROACH の見出し色を `text-muted` → `text-ink`**: 灰色だと視認性が低かったため黒に
4. **APPROACH を円形バッジ → 角丸カードへ**: 円形は装飾的すぎて Service カードと統一感を欠いたため、3枚の白カードに変更
5. **ABOUT を白 → 青背景に**: 6セクション中ベージュ/白ばかりで単調だったため、ABOUT を `bg-accent` でアクセント区間に
6. **ヒーロー画像に sepia フィルター**: Unsplash 原画像がやや寒色寄りで、ベージュ基調から浮いていたため `filter: sepia(15%) saturate(0.9) brightness(0.97)` で暖色に補正
7. **ラベルは `font-black` 必須**: Noto Sans JP 700 が macOS subpixel rendering で細く見える事象が複数回発生。重要ラベルは 900 を当てる
8. **絵文字装飾は不採用**: TIME SAVED / CAPACITY / ANNUAL ROI に ✏️ 📅 💰 を試したが「情報密度が下がった」と判断し撤回。シンプルが正
9. **「2026年5月開業」を全箇所から削除**: ヒーロー下のサブ情報行 + フッターから撤去（開業時期は事業実態確認には必須でない）

---

## 13. 編集タブー（よくある落とし穴）

- **mailto: リンクは復活させない** → 必ず Tally popup を使う
- **`font-bold` を重要ラベルに使わない** → `font-black` を選ぶ
- **`text-muted` を見出しに使わない** → 視認性が落ちる
- **媒体実名（Indeed / びびなび）を書かない** → 匿名化マップに沿って差し替え
- **「ROI 約X倍」と曖昧に書かない** → 分子÷分母を必ず添える
- **代表者の個人名を出さない** → JSON-LD でも個人名なし
- **APPROACH と About の強み三角の重複を復活させない** → 削除済みのものを再度入れない

---

## 14. 連絡先

- 事業者: 個人事業主・Chabi
- メール: jumpei_kondo@chabi.jp
- 公式 LP: [chabi.jp](https://chabi.jp/)

---

引き継ぎは以上です。Antigravity 等の別環境で着手する際は、この HANDOFF.md と `git log --oneline` を眺めればコンテキスト復元可能です。
