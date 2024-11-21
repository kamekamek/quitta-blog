---
title: bolt.newをローカル環境で動かす完全ガイド
tags:
  - AI
  - Bolt
private: false
updated_at: '2024-11-21T22:12:16+09:00'
id: 3ae9dbf084c2ef1584ff
organization_url_name: null
slide: false
ignorePublish: false
---
# bolt.newをローカル環境で動かす完全ガイド

bolt.newは、テキストのみでフルスタックアプリケーションを開発できる革新的なAIツールです。この記事では、MacでのローカルセットアップからAnthropicのAPI設定まで、詳しく解説します。

## 前提条件

以下のソフトウェアが必要です。

- Node.js (v20.15.1以上)
- pnpm (v9.4.0以上)
- Git

また、今回はMacOSでのセットアップ前提としています。

ターミナルを立ち上げて、以下の手順を実行してください。

## Node.jsのインストール

### Homebrewを使用したインストール

```bash
brew install node

```

インストールが完了したら、以下のコマンドでバージョンを確認します：

```bash
node -v

```

バージョンが表示されれば、インストール成功です。

## pnpmのインストール

Node.jsがインストールされていることを確認してから、以下の手順で進めます：

### Homebrewを使用したインストール方法

```bash
brew install pnpm

```

インストール後、以下のコマンドでバージョンを確認します：

```bash
pnpm -v

```

正しいバージョン番号が表示されれば、インストール完了です。

## Gitのインストール

### Homebrewを使用したインストール

```bash
brew install git

```

インストール後、以下のコマンドで確認します：

```bash
git --version

```

### Gitの初期設定

インストール後、以下の設定を行います：

```bash
git config --global user.name "あなたの名前"
git config --global user.email "あなたのメールアドレス"

```

この設定により、Gitでの作業履歴に名前とメールアドレスが記録されます。

## インストール確認

すべてのインストールが完了したら、以下のコマンドで各ツールのバージョンを確認します：

```bash
node -v    # Node.jsのバージョン確認
pnpm -v    # pnpmのバージョン確認
git --version    # Gitのバージョン確認

```

それぞれのコマンドが正常に動作し、バージョン番号が表示されれば、すべてのセットアップは完了です。


## セットアップ手順

ターミナルを立ち上げて、以下の手順でセットアップを行う。

### 1. リポジトリのクローン
bolt.newのリポジトリ
https://github.com/stackblitz/bolt.new

```bash
git clone <https://github.com/coleam00/bolt.new-any-llm.git>
cd bolt.new-any-llm

```

### 2. 依存関係のインストール

```bash
pnpm install

```

### 3. AnthropicのAPIキー取得

Anthropic公式サイトから、APIのシークレットキーを取得する。

公式サイト：https://console.anthropic.com/login?returnTo=/?

※今回は、説明を省きます。

### 4. 環境設定ファイルの作成

```bash
touch .env.local

```

.env.localファイルに以下の内容を追加：

```
ANTHROPIC_API_KEY=<取得したAPIキーを入力>
VITE_LOG_LEVEL=debug

```

### 5. アプリケーションのビルドと起動

```bash
pnpm run build
pnpm run start

```

ビルドが完了すると、「built in X.XXs」というメッセージが表示されます[1]。

## 動作確認

ブラウザで`http://localhost:8788`にアクセスし、bolt.newの画面が表示されることを確認します。

## トラブルシューティング

- ビルドエラーが発生した場合は、依存関係が正しくインストールされているか確認してください
- APIキーが認識されない場合は、.env.localファイルの内容を再確認してください
- Gitのエラーが出た場合は、Gitがインストールされているか確認してください

## 注意事項

- AnthropicのAPIは商用利用規約の対象となっています
- APIキーは一度しか表示されないので、必ず安全な場所に保存してください[4]
- 環境設定ファイルを変更した場合は、必ずビルドを再実行する必要があります

※この記事は、一部AIを使用して執筆しております。


