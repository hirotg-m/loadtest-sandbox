# loadtest-sandbox

クラウド上の Web サーバーに対して、コンテナを使って負荷試験（ロードテスト）を行うための練習用リポジトリです。

## 目的

- EC2 上で複数コンテナを起動し、疑似クライアントから同時アクセスを発生させる練習をする
- k6 / JMeter / Locust などの負荷試験ツールの基礎を身につける
- 負荷試験用コンテナ（Docker イメージ）の作成・実行方法を整理する
- 後から同じ構成を再現できるよう、設定や手順をコードとして残す

## 想定シナリオ

- 1 台の EC2 インスタンス上に Docker コンテナを複数起動
- 各コンテナの中で、複数の疑似クライアント（スレッド／VUs）を動かして Web サーバーにアクセス
- 例：  
  - EC2 上に 10 コンテナ  
  - 各コンテナで 10 クライアント相当を起動  
  - 合計 100 クライアント相当の同時アクセスを発生させる

## ディレクトリ構成（案）

※最初は空、または最小限のファイルから始めて、順次追加していくことを想定しています。

```text
loadtest-sandbox/
  ├─ k6/           # k6 を使った負荷試験用コンテナ & スクリプト
  ├─ jmeter/       # Apache JMeter 用コンテナ & テストプラン
  ├─ locust/       # Locust 用コンテナ & スクリプト
  ├─ docs/         # メモ、検証結果、アーキテクチャ図など
  └─ README.md
```

各ツール用ディレクトリには、次のようなファイルを置く想定です。

- `Dockerfile`：負荷試験ツールを動かすコンテナイメージ定義
- サンプルスクリプト：`basic-test.js`（k6）や `basic-test.jmx`（JMeter）など
- `README.md`：そのツール特有の使い方メモ

## 前提環境

ローカル or EC2 上で、以下が使えることを前提とします。

- Git
- Docker（または互換コンテナランタイム）
- （必要に応じて）AWS CLI / docker-compose / ECS CLI など

## 使い方（基本的な流れ）

1. このリポジトリを clone します。

   ```bash
   git clone https://github.com/<your-account>/loadtest-sandbox.git
   cd loadtest-sandbox
   ```

2. 試したいツールのディレクトリに移動します（例：k6）。

   ```bash
   cd k6
   ```

3. コンテナイメージをビルドします。

   ```bash
   docker build -t loadtest-k6 .
   ```

4. ターゲットの Web サーバー URL などを環境変数で指定してコンテナを実行します。

   ```bash
   docker run --rm \
     -e TARGET_URL="https://example.com" \
     loadtest-k6
   ```

   ※実際のコマンドやオプションは、各ディレクトリ内の `README.md` に整理していきます。

## メモ・方針

- 最初はシンプルな HTTP GET のみのシナリオから始める
- 慣れてきたらログインや API 呼び出しなど、複数ステップのシナリオに発展させる
- 「負荷生成側（EC2 / コンテナ）がボトルネックになっていないか」をメトリクスで確認する
- 本番向けというより、「手を動かして学ぶためのサンドボックス」として使う

## 今後の TODO（例）

- [ ] k6 用の最小サンプル（Dockerfile + `basic-test.js`）を追加
- [ ] JMeter 用の最小サンプル（Dockerfile + `.jmx`）を追加
- [ ] Locust 用の最小サンプル（Dockerfile + `locustfile.py`）を追加
- [ ] EC2 上で複数コンテナを一括で起動するサンプル（docker-compose など）を追加
- [ ] 実行結果のログ・グラフの取り方を整理
