---
title: browser-use-webui入門ガイド
tags:
  - AI
  - AIエージェント
  - browser-use
  - browser-use-webui
private: false
updated_at: '2025-01-05T17:30:39+09:00'
id: 37e4442ae6241de8f7fe
organization_url_name: null
slide: false
ignorePublish: false
---
# browser-use入門ガイド

この記事では、AIエージェントフレームワークであるbrowser-use、特にbrowser-use-webuiの使い方について解説します。GitHubのリポジトリのクローンから始まり、Pythonの仮想環境を用いたセットアップ、Deep Seek APIの利用、請求情報まで、初学者の方でも理解しやすいようにステップバイステップで説明していきます。

## browser-useとは？

近年、目覚ましい発展を許しているAI技術の中でも、特に注目を集めているのが\*\*大規模言語モデル(LLM)\*\*です。LLMは、人間のように自然な文章を生成したり、質問に答えたり、翻訳を行ったりと、さまざまなタスクをこなすことができます。

browser-useは、このLLMをウェブブラウザ上で動作させるためのフレームワークです。ユーザーは、ブラウザ上で指示を出すだけで、LLMにさまざまなタスクを実行させることができます。例えば、特定のウェブサイトから情報を収集する、ウェブサイト上で自動的に操作を行う、質問に対する回答をウェブ検索で探し出すといったことが可能です。

当初のLLMはAPI経由で利用することが一般的でした。しかし、APIの利用には専門的な知識が必要となる場合があり、初学者にとってはハードルが高いものでした。browser-useは、このハードルを下げ、より多くの人がLLMの恩恵を受けられるようにすることを目指して開発されました。すなわち、APIの知識がなくても、ブラウザを操作するだけでAIを利用できるようになるのです。

ウェブブラウザと検索エンジンの違いについて少し補足しておきましょう。ウェブブラウザはインターネット上のウェブサイトを閲覧するためのアプリケーションソフトウェアです。2一方、検索エンジンは、特定のキーワードに基づいてウェブページ、画像、動画などを探し出すためのシステムです。Googleなどがその代表例です。2 browser-useは、このウェブブラウザ上で動作し、AIを利用してさまざまなタスクを自動化します。

## browser-use-webui を使ってみよう

browser-use-webuiは、browser-useをウェブブラウザ上で簡単に操作するためのGUIツールです。Pythonの仮想環境を用いることで、手身にセットアップすることができます。

### ステップバイステップ

#### ステップ 0: リポジトリのクローン

1. さず、Browser-use-webuiの[GitHubリポジトリ](https://github.com/warmshao/browser-use-webui)をクローンします。
2. ターミナルまたはコマンドプロンプトを開きます。
3. 以下のコマンドを実行してリポジトリをクローンします。

```bash
git clone https://github.com/warmshao/browser-use-webui.git
```

4. クローンしたディレクトリに移動します。

```bash
cd browser-use-webui
```

#### ステップ 1：Python仮想環境の作成

1. Pythonの仮想環境を作成します。仮想環境を作成することで、Browser-useに必要なライブラリを他のプロジェクトに影響を与えることなくインストールすることができます。
2. ターミナルまたはコマンドプロンプトを開き、以下のコマンドを実行します。

```bash
python -m venv .venv
```

3. 以下のコマンドを実行して仮想環境を有効化します。

```bash
# Windowsの場合
.venv\Scripts\activate

# macOS/Linuxの場合
source .venv/bin/activate
```

#### ステップ 2： browser-use-webuiのインストール

1. 仮想環境が有効化されていることを確認します。
2. 以下のコマンドを実行してbrowser-useと必要な依存関係をインストールします。

```bash
pip install browser-use-webui

# playwrightのインストール
playwright install

# 依存関係のインストール
pip install -r requirements.txt
```

#### ステップ 3：環境変数の設定

1. browser-use-webuiを使用するためには、いくつかの環境変数を設定する必要があります。
2. browser-use-webuiのルートディレクトリに`.env`ファイルを作成します。
3. `.env.example`ファイルを`.env`ファイルにコピーします。
4. `.env`ファイルを編集し、LLMのAPIキーなどの環境変数を設定します。例として、Deep Seek APIを利用する場合には、以下のようにAPIキーを記述します。

```plaintext
DEEPSEEK_API_KEY=your_deepseek_api_key
```

5. 自分のブラウザを使用する場合は、`CHROME_PATH`にブラウザの実行ファイルパスを、`CHROME_USER_DATA`にブラウザのユーザーデータディレクトリを設定します。

Windowsの場合
```plaintext
CHROME_PATH=C:\Program Files\Google\Chrome\Application\chrome.exe
CHROME_USER_DATA=C:\Users\<YourUsername>\AppData\Local\Google\Chrome\User Data
```

Macの場合:
```plaintext
CHROME_PATH=/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome
CHROME_USER_DATA=~/Library/Application\ Support/Google/Chrome
```

#### ステップ 4：WebUIの実行

以下のコマンドを実行してWebUIを起動します。

```bash
python webui.py --ip 127.0.0.1 --port 7788
```

#### ステップ 5：WebUIへのアクセス

ウェブブラウザを開き、[http://127.0.0.1:7788](http://127.0.0.1:7788) にアクセスします。これでbrowser-use-webuiが起動し、AIモデルを利用する準備が整いました。

#### ステップ 6：独自のブラウザの使用

1. Chrome以外のブラウザを使用する場合は、以下の手順に従います。
   - すべてのChromeウィンドウを閉じます。
   - FirefoxやEdgeなどのChrome以外のブラウザでWebUIを開きます。
2. ブラウザ設定で「Use Own Browser」オプションをオンにします。この設定により、エージェントの実行時にChromeのデータが使用されるのを防ぐことができます。

### Model Name の入力方法

Model Nameは、browser-use-webuiのインターフェース上で指定することができます。具体的な入力方法は、以下の通りです。

1. browser-use-webuiにアクセスします。
2. 画面上部の「Model」タブをクリックします。
3. 「Model Name」フィールドに、利用したいAIモデルのモデル名を入力します。

### Deep Seek API を使う際のつまづきポイント

Deep Seek APIを利用する際に、いくつか注意すべき点があります。以下の表に、よくある問題とその解決策をまとめました。

| Issue                      | Solution                                                                                       |   |
| -------------------------- | ---------------------------------------------------------------------------------------------- | - |
| APIキーが正しく設定されていない          | APIキーを確認し、正しく設定されていることを確認してください。                                                               |   |
| APIクレジットが不足している            | APIクレジットを購入しましょう [https://platform.deepseek.com/top\_up](https://platform.deepseek.com/top_up) |   |
| UI上でModel Nameが正しく入力されていない | Model Nameはdeepseek-chatです。                                                                    |   |
| use visionにチェックが入っている      | DeepSeekを使用する際はオフにする                                                                           |
| エージェントが正しく動作しない            | ターミナルでログを確認し、詳細なエラー情報を確認してください。                                                                |   |

###

## まとめ

この記事では、browser-useの基本的な使い方から、つまづきポイント、課金情報までを解説しました。browser-useは、AIモデルをより身近に、より使いやすくするためのツールです。

browser-useを使うことで、これまでAPIの知識が必要だった大規模言語モデルを、ブラウザ操作だけで利用できるようになります。browser-use-webuiを使えば、GitHubからリポジトリをクローンし、Pythonの仮想環境を構築し、必要な環境変数を設定することで、簡単にbrowser-useを始めることができます。

記事で紹介した手順を参考に、browser-use-webuiをセットアップし、実際にAIモデルを動かしてみてください。その際には、「use vision」オプションをオフにすること、Deep Seek APIを利用する際の注意点などを忘れずに確認しましょう。

browser-useは、まだ発展途上のツールですが、その可能性は非常に大きいと言えるでしょう。今後、browser-useがどのように進化していくのか、そして私たちの生活にどのような影響を与えていくのか、注目していきたいところです。

※この記事は、一部AIを用いて作成しています。
