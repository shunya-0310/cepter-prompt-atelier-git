# Cepter Prompt Atelier Git Deploy

このフォルダはCloudflare PagesのGit連携デプロイ用です。
DashboardのDirect Upload用フォルダとは分けています。

## 狙い

Git連携版では、生成プロンプト本文を短くし、AIが読むべき情報をDossier URLに集約します。

```text
https://cepter-atelier.com/ai/dossier/<token>.md
```

このURLはPages FunctionsでMarkdownを直接返します。
Dossierには以下を含めます。

- 相談内容
- ブック内カード
- 所持している未採用候補カード
- ユーザーのストラテジー
- 基本ルールと評価軸の要約
- 秘蔵ナレッジ
- 公開ナレッジ

## Cloudflare Pages 設定

Git連携でこのフォルダをリポジトリとして接続します。

- Framework preset: None
- Build command: 空欄
- Build output directory: `/`
- Root directory: リポジトリ直下

`functions/` ディレクトリはCloudflare Pages Functionsとして認識されます。

## 主な動的URL

- `/ai/dossier/<token>.md`
  - 共有コンテキストを読み、公開ナレッジを追記したMarkdownを返します。
- `/ai/knowledge/public.md`
  - 公開ナレッジだけをMarkdownで返します。

## 環境変数

未設定でも公開anon keyのデフォルト値で動きます。
運用ではCloudflare PagesのEnvironment variablesに以下を設定できます。

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`

## デプロイ後の確認

1. トップページを開く。
2. `プロンプト生成` を押す。
3. 生成されたプロンプトのDossier URLが `/ai/dossier/<token>.md` になっていることを確認する。
4. Dossier URLをブラウザで開き、Markdown本文が表示されることを確認する。
5. ChatGPTなどに短いプロンプトを投げ、Dossier内容を取得できるか確認する。
