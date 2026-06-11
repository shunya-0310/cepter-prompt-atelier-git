# Cepter Prompt Atelier Git Deploy

このフォルダはCloudflare WorkersのGit連携デプロイ用です。
DashboardのDirect Upload用フォルダとは分けています。

## 狙い

Git連携版では、生成プロンプト本文を短くし、AIが読むべき情報をDossier URLに集約します。

```text
https://cepter-atelier.com/ai/dossier/<token>.md
```

このURLはWorkerがMarkdownを直接返します。
Dossierには以下を含めます。

- 相談内容
- ブック内カード
- 所持している未採用候補カード
- ユーザーのストラテジー
- 基本ルールと評価軸の要約
- 秘蔵ナレッジ
- 公開ナレッジ

## Cloudflare Workers 設定

このリポジトリは `wrangler.jsonc` を使って、Workers + Static Assetsとしてデプロイします。

- Build command: 空欄
- Deploy command: `npx wrangler deploy`
- Root directory: リポジトリ直下
- Static assets directory: `.` (`wrangler.jsonc` の `assets.directory`)
- Worker entry: `src/worker.js`

`functions/` ディレクトリは、Pages Functions用の旧構成です。
現在のCloudflare側が `npx wrangler deploy` を実行している場合、動的URLは `src/worker.js` が担当します。

## 主な動的URL

- `/ai/dossier/<token>.md`
  - 共有コンテキストを読み、公開ナレッジを追記したMarkdownを返します。
- `/ai/knowledge/public.md`
  - 公開ナレッジだけをMarkdownで返します。

## ユーザーに見せるURL

`*.workers.dev` はCloudflareの仮URLです。
アカウントサブドメインが入るため、公開導線や生成プロンプトでは使いません。

本番ではWorkerにカスタムドメインを割り当てます。

```text
https://cepter-atelier.com/
```

Cloudflare Dashboardでは、Workerの `Settings > Domains & Routes` から `cepter-atelier.com` をCustom Domainとして追加します。
カスタムドメインで動作確認できたら、同じ画面で `workers.dev` をDisableしておくと、仮URLを表に出しにくくできます。

## 環境変数

未設定でも公開anon keyのデフォルト値で動きます。
運用ではCloudflare WorkersのVariablesに以下を設定できます。

- `SUPABASE_URL`
- `SUPABASE_ANON_KEY`

## デプロイ後の確認

1. GitHubに `wrangler.jsonc` と `src/worker.js` が入っていることを確認する。
2. CloudflareのDeploymentsで再デプロイが成功していることを確認する。
3. `https://cepter-atelier.com/ai/knowledge/public.md` を開き、Markdown本文が表示されることを確認する。
4. トップページを開く。
5. `プロンプト生成` を押す。
6. 生成されたプロンプトのDossier URLが `/ai/dossier/<token>.md` になっていることを確認する。
7. Dossier URLをブラウザで開き、Markdown本文が表示されることを確認する。
8. ChatGPTなどに短いプロンプトを投げ、Dossier内容を取得できるか確認する。
