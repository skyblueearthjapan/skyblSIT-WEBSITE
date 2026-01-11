# SEO技術・運用ガイドライン (`docs/seo-guidelines.md`)

## 1. 基本方針

本サイトは「中小企業 DX」「業務効率化 AI」などのキーワードでの検索流入を最重要視する。
**「AIエージェントが自動的にSEO施策を適用できる状態」** を構築することをゴールとする。

### 技術スタック前提
- **Rendering**: Static Site Generation (SSG) - Astro / Next.js
- **Hosting**: Vercel / Cloudflare Pages / AWS Amplify
- **Performance**: Lighthouse スコア All 95+ を準拠

---

## 2. メタデータ設計

各ページは以下のメタデータを必ず出力する。

### `<head>` 必須タグ
```html
<title>{title} | {SiteName}</title>
<meta name="description" content="{description}">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="canonical" href="{canonicalUrl}">
<meta name="robots" content="index, follow">
```

### Open Graph Protocol (OGP)
SNSシェア時の表示最適化。
```html
<meta property="og:type" content="{article | website}">
<meta property="og:title" content="{title}">
<meta property="og:description" content="{description}">
<meta property="og:url" content="{currentUrl}">
<meta property="og:image" content="{featuredImage}">
<meta name="twitter:card" content="summary_large_image">
```

### JSON-LD (構造化データ)
Google検索へのリッチリザルト対応。

**記事詳細ページ (Article):**
```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "{title}",
  "image": ["{featuredImage}"],
  "datePublished": "{pubDate}",
  "author": {
    "@type": "Person",
    "name": "{authorName}"
  }
}
```

**パンくずリスト (BreadcrumbList):**
全ページで自動生成すること。

---

## 3. URL・ルーティング設計

- **正規化**: 末尾スラッシュ（Trailing Slash）は「あり」または「なし」で統一し、逆のパターンからは301リダイレクトを行う。
- **階層**: URLは可能な限り浅く保つが、意味的な階層構造は維持する。
    - OK: `/insights/ai-productivity-tips`
    - NG: `/insights/2024/01/category/ai/ai-productivity-tips`
- **言語**: 日本語URLは使用せず、半角英数字（kebab-case）のslugを使用する。
    - `docs/content-structure.md` のファイル名ルールに従う。

---

## 4. 内部リンク戦略 (Internal Linking)

AIエージェントは、記事作成時に以下の内部リンク構造を意識してリンクを埋め込むこと。

### ハブ＆スポーク構造
- **事業ページ (Hub)**: 事業詳細ページ（親）
    - **導入事例 (Spoke)**: その事業の導入事例へのリンク
    - **関連ナレッジ (Spoke)**: その事業に関連する解説記事へのリンク

### 記事内リンク (Contextual Link)
- 専門用語が出た際は、用語解説記事へリンクする。
- 課題提起（「手入力が面倒」）の直後に、解決策記事（「OCR活用法」）へリンクする。
- 記事下部には必ず「無料相談」または「ホワイトペーパー」へのCTAを設置する。

---

## 5. 画像・メディア最適化

- **フォーマット**: WebP または AVIF を推奨。
- **Lazy Loading**: ファーストビュー以外の画像は `loading="lazy"` を付与。
- **Alt属性**: 装飾以外の全ての画像に、具体的な内容を示す `alt` テキストを付与する。
    - NG: `alt="image"`
    - OK: `alt="iPadで在庫管理アプリを操作する倉庫スタッフ"`

---

## 6. サイトマップ・Robots

- **sitemap.xml**: ビルド時に全公開ページを含めて自動生成する。`lastmod` を付与すること。
- **robots.txt**: 基本的に全許可。admin系や下書きプレビューのみ `Disallow`。

---

## 7. AIエージェントへの指示

AIエージェントがコンテンツを生成・修正する際は、以下のチェックリストを裏で実行すること。

1. **Title/Description**: 文字数は適切か？（32文字 / 120文字）
2. **Hタグ階層**: `h1` は1つだけか？ `h2` -> `h3` の順序は正しいか？
3. **Alt**: 画像に説明文はあるか？
4. **Links**: 孤立ページになっていないか？（親または関連ページへのリンクがあるか）
