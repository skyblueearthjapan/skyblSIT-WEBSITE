# コンテンツ設計・管理仕様書 (`docs/content-structure.md`)

## 1. 概要
本ドキュメントは、コーポレートサイト内のコンテンツ（ブログ、ニュース、事業紹介など）の管理方法、ディレクトリ構造、および各ファイルのFrontmatter定義を規定する。

**目的:**
- AIエージェントが迷わずに記事を作成・更新できるようにする
- サイト全体で統一されたメタデータを保証する
- 将来的なCMS移行やヘッドレスCMS連携を見越した構造にする

---

## 2. ディレクトリ構造

コンテンツは `src/content/` 配下に格納する。（フレームワークは Astro / Next.js 等を想定、Collection機能の利用を前提とする）

```
src/content/
├── business/       # 事業紹介・サービス詳細（固定ページに近い）
├── cases/          # 導入事例・実績
├── news/           # お知らせ・プレスリリース
├── insights/       # ナレッジ・ブログ・技術記事
├── faq/            # よくある質問
└── authors/        # 執筆者プロフィール
```

### 命名規則
- ファイル名: `YYYY-MM-DD-slug.md` または `slug.mdxy`
- 画像格納: 記事と同じディレクトリ名でフォルダを作成するか、`public/images/content/{collection}/{slug}/` を使用

---

## 3. Frontmatter 定義

各コンテンツタイプごとの必須・推奨フィールドを定義する。

### 3.1. 共通フィールド (Base Schema)
すべてのコンテンツに最低限必要なフィールド。

```yaml
---
title: "記事タイトル（32文字前後推奨）"
description: "記事の要約・抜粋（120文字前後推奨）。SEOのdescriptionに使用される。"
pubDate: 2024-01-01  # 公開日
updatedDate: 2024-01-05  # 最終更新日（任意）
isDraft: false      # 下書きフラグ（trueの場合はビルドから除外）
featuredImage: "./hero.jpg" # OGP・アイキャッチ画像
---
```

### 3.2. Insights (ナレッジ・ブログ)
技術記事や考え方を発信するメインのオウンドメディア領域。

```yaml
---
# 共通フィールド
title: "中小企業こそ生成AIを「検索」として使ってはいけない"
description: "ChatGPTを単なる検索エンジンの代わりとして使うと失敗します。業務フローに組み込むための具体的なプロンプト設計思考を解説。"
pubDate: 2024-03-20
featuredImage: "/images/insights/ai-search-trap.png"

# Insights固有フィールド
category: "AI活用"  # 1つのみ選択（AI活用 / DX推進 / アプリ開発 / 経営マインド）
tags: ["ChatGPT", "プロンプトエンジニアリング", "業務効率化"] # 複数可
author: "yuji-imaizumi" # authorsコレクションのID
relatedPosts: ["2024-02-15-ai-basics", "2024-01-10-dx-start"] # 関連記事のslug（AIによる自動推奨も可）
complexity: "Beginner" # Beginner / Intermediate / Advanced
---
```

### 3.3. Cases (導入事例)
顧客の成功事例。

```yaml
---
title: "【株式会社〇〇様】アナログな在庫管理をAppSheetで2週間でアプリ化"
description: "手書き伝票での管理に限界を感じていた卸売業のお客様。Google Workspace連携でリアルタイム在庫管理を実現しました。"
pubDate: 2024-02-10
featuredImage: "/images/cases/company-a-hero.jpg"

# Cases固有フィールド
clientName: "株式会社〇〇"
industry: "卸売・小売"
serviceUsed: ["AppSheet", "Spreadsheet"]
outcome: ["作業時間50%削減", "転記ミスゼロ化"]
---
```

### 3.4. Business (事業紹介)
サービスLP的な役割。

```yaml
---
title: "AppSheet開発支援サービス"
description: "Google Workspaceを活用し、ノーコードで現場に馴染む業務アプリを高速開発します。"
order: 1 # 表示順序
icon: "appsheet" # アイコン識別子
ctaText: "無料相談を申し込む"
ctaLink: "/contact"
---
```

---

## 4. タグ・カテゴリ運用ルール

### カテゴリ（厳格）
カテゴリは以下のリストに限定し、無秩序な追加を禁止する。変更する場合は本ドキュメントを更新すること。

1. **AI活用**: 生成AI、LLM、自動化関連
2. **DX推進**: 組織論、導入プロセス、チェンジマネジメント
3. **アプリ開発**: AppSheet, GAS, ノーコードツール技術論
4. **データ活用**: Looker Studio, 分析, 可視化
5. **経営マインド**: 中小企業経営、生産性、組織作り
6. **ニュース**: お知らせ（newsコレクションへ移行推奨だが、insightsに含める場合もあり）

### タグ（柔軟）
タグは具体的かつ検索されやすいキーワードを採用する。
- ツール名（ChatGPT, Gemini, Slack）
- 業種（建設業, 介護, 飲食）
- 課題（人手不足, 採用, ペーパーレス）

---

## 5. AIエージェントによる記事作成ワークフロー

AIエージェントが記事をドラフトする際は、以下のステップを踏むこと。

1. **トピック決定**: ターゲット読者（中小企業経営者・現場責任者）の課題を設定。
2. **Frontmatter生成**: 上記スキーマに従い、適切なメタデータを埋める。特に `relatedPosts` は既存記事から適切に選定する。
3. **構成案作成**: H2, H3レベルの見出しを作成。
4. **執筆**: `docs/site-design.md` の思想（「現場負担をかけない」「難しくしない」）に基づき執筆。
5. **ファイル保存**: 適切なディレクトリに、命名規則に従って保存。

---

## 6. チェックリスト（公開前）

- [ ] Frontmatterの必須項目は埋まっているか？
- [ ] 画像パスは正しいか？
- [ ] 誤字脱字、「てにをは」のチェック
- [ ] 専門用語には補足説明があるか？（ターゲットは非エンジニアが多いため）
