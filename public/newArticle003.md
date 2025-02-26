---
title: browser-use-webui入門ガイド
tags:
  - AI
  - AIエージェント
  - browser-use
  - browser-use-webui
private: false
updated_at: '2025-01-08T22:20:01+09:00'
id: 37e4442ae6241de8f7fe
organization_url_name: null
slide: false
ignorePublish: false
---
# browser-use-webui入門ガイド

この記事では、AIエージェントフレームワークであるbrowser-use、特にbrowser-use-webuiの使い方について解説します。GitHubのリポジトリのクローンから始まり、Pythonの仮想環境を用いたセットアップ、Deep Seek APIの利用まで、初学者の方でも理解しやすいようにステップバイステップで説明していきます。

## browser-useとは？

近年、目覚ましい発展を遂げているAI技術の中でも、特に注目を集めているのが大規模言語モデル(LLM)です。LLMは、人間のように自然な文章を書いたり、質問に答えたり、翻訳をしたりと、様々なことができるんです。

browser-useは、このLLMをウェブブラウザ上で簡単に使えるようにするためのフレームワークです。ブラウザ上で指示を出すだけで、LLMにいろいろな作業をお願いすることができます。例えば、ウェブサイトから情報を集めたり、サイト上で自動的に操作をしたり、質問の答えをウェブ検索で探したりといったことが可能なんです。

当初のLLMはAPI経由で利用することが一般的でした。しかし、APIの利用には専門的な知識が必要となる場合があり、初学者にとってはハードルが高いものでした。browser-useは、このハードルを下げ、より多くの人がLLMの恩恵を受けられるようにすることを目指して開発されました。すなわち、APIの知識がなくても、ブラウザを操作するだけでAIを利用できるようになるのです。

ウェブブラウザと検索エンジンの違いについて少し補足しておきましょう。ウェブブラウザはインターネット上のウェブサイトを閲覧するためのアプリケーションソフトウェアです。一方、検索エンジンは、特定のキーワードに基づいてウェブページ、画像、動画などを探し出すためのシステムです。Googleなどがその代表例です。browser-useは、このウェブブラウザ上で動作し、AIを利用してさまざまなタスクを自動化します。

## browser-use-webui を使ってみよう

browser-use-webuiは、browser-useをウェブブラウザ上で簡単に操作するためのGUIツールです。Pythonの仮想環境を用いることで、手身にセットアップすることができます。


#### ステップ 0: リポジトリのクローン

1. まず、Browser-use-webuiの[GitHubリポジトリ](https://github.com/browser-use/web-ui)をクローンします。
2. ターミナルまたはコマンドプロンプトを開きます。
3. 以下のコマンドを実行してリポジトリをクローンします。

```bash
git clone https://github.com/browser-use/web-ui.git
```

4. クローンしたディレクトリに移動します。

```bash
cd web-ui
```

#### ステップ 1：Python仮想環境の作成

1. Pythonの仮想環境を作成します。仮想環境を作成することで、Browser-useに必要なライブラリを他のプロジェクトに影響を与えることなくインストールすることができます。
2. ターミナルまたはコマンドプロンプトを開き、以下のコマンドを実行します。

```bash
uv venv --python 3.11
```

3. 以下のコマンドを実行して仮想環境を有効化します。

```bash
# Windowsの場合
.venv\Scripts\activate

# macOS/Linuxの場合
source .venv/bin/activate
```

#### ステップ 2： 依存関係のインストール


```bash

# playwrightのインストール
playwright install

# 依存関係のインストール
pip install -r requirements.txt
```

#### ステップ 3：環境変数の設定
1. web-uiを使用するためには、いくつかの環境変数を設定する必要があります。
2. web-uiのルートディレクトリに`.env`ファイルを作成します。
3. `.env.example`ファイルを`.env`ファイルにコピーします。
4. `.env`ファイルを編集し、LLMのAPIキーなどの環境変数を設定します。例として、Deep Seek APIを利用する場合には、以下のようにAPIキーを記述します。

```plaintext
DEEPSEEK_API_KEY=your_deepseek_api_key
```

5. （オプション）自分のブラウザを使用する場合は、`CHROME_PATH`にブラウザの実行ファイルパスを、`CHROME_USER_DATA`にブラウザのユーザーデータディレクトリを設定します。

Windowsの場合
```plaintext
CHROME_PATH="C:\Program Files\Google\Chrome\Application\chrome.exe"
CHROME_USER_DATA="C:\Users\YourUsername\AppData\Local\Google\Chrome\User Data"
```
注: YourUsernameWindows システムの場合は実際の Windows ユーザー名に置き換えてください。

Macの場合:
```plaintext
CHROME_PATH="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
CHROME_USER_DATA="~/Library/Application Support/Google/Chrome/Profile 1"
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



## まとめ
browser-useを使えば、これまでは難しかったAIによるブラウザ操作が、簡単にできるようになるかもしれません。この記事で紹介した手順に従えば、誰でも簡単にbrowser-use-webuiを使い始めることができますよ。AIツールは日々進化していて、browser-useもその一つです。まだまだ発展途上ですが、これからどんな風に私たちの生活を便利にしてくれるのか、とても楽しみですね。

※この記事は、一部AIを用いて作成しています。
