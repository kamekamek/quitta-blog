---
title: sattoを活用した一括処理スキルの実践解説
tags:
  - タスク管理
  - 一括処理
  - satto
  - Notion連携
private: false
updated_at: '2025-01-05T14:52:47+09:00'
id: 31521377f707b46e923d
organization_url_name: null
slide: false
ignorePublish: false
---


# **sattoを活用した一括処理スキルの実践解説**

今回は、私が実際に作成した「✅タスクをリストにしてNotionのGTDに転記してくれるスキル」を例にして、アプリノードの一括処理機能ををご紹介します。

---

## **1. sattoの基本情報**

**sattoとは？**  
sattoはソフトバンクが提供する生成AIエージェントで、直感的な操作で業務を効率化できるツールです。プロンプト入力が不要で、GoogleカレンダーやGmailなどの外部サービスともシームレスに連携可能なツールです。

### **主なメリット**
- **簡単操作**: プログラムやAI知識がなくても直感的に使える。
- **外部連携**: GoogleやSaaSツールと連携し、作業を一元化。
- **カスタマイズ性**: ユーザーが独自のスキルを作成・共有可能。

---

## **2. スキルの概要**

今回紹介するスキルの目的は、「タスクをリスト化し、NotionのGTDデータベースに転記すること」です。非常に地味なタスクですが、チャットや文章でタスクを投げられたり、自分が洗い出したタスクを抽出して、Notionデータベースに登録することができます。

---

## **3. 実装例**

**使用手順**
1. **文章選択**: 登録したいタスクを文章から選択し、`Ctrl + A`でコピー。
2. **タスク実行**: sattoで作成したスキルを実行。
3. **AIノードで処理**: AIノードが入力文章をタスクとして分解し、配列で出力。
4. **Notionに転記**: 配列内のタスクを一つずつNotionのデータベースに登録。  

2~3を自動で行ってくれます。

---

## **4. 各ノードの仕組み**

### **入力ノード**  
入力文章をそのまま次のノードへ送信します。特別な設定は不要です。

### **AIノード（タスク抽出）**  
ここがスキルの要となる部分です。

#### プロンプト例:
```jsx
あなたはタスク抽出システムです。次の手順に従ってユーザーの文章から複数のタスクを抽出し、配列で出力してください。
手順:
1. ユーザーの入力文章を読みます。
2. 文章の中から「タスク」と見なせる個々の行動や行為を特定します。この際、各行動は明確かつ具体的なものに限ります。
3. 抽出したタスクを個別の要素として配列に追加します。
4. 配列形式で抽出されたタスクを出力します。

入力文章: {{/入力文章をコンテキストとして選択}}

# 補足
- 指示の復唱はしないでください。
- 自己評価はしないでください。
- 結論やまとめは書かないでください。
- 最終成果物以外は出力しないでください。
- 出力結果をダブルクォーテーション("")で囲わないでください。

```

このプロンプトにより、AIノードが入力文章を解析し、タスクを個別の要素として配列で出力します。

### **アプリノード（Notion API処理）**
#### 事前準備
1. Notionとsattoを連携。
2. Notionに「フルページ」のデータベースを作成。   
※今回は、プロパティとして、名前（テキスト）とタグ（セレクト）を追加している
3. データベースIDを取得（`https://www.notion.so/XXX?v=YYY` の `XXX` 部分）。

#### 各パラメータの設定
- 一括処理モードをオンにする 
- **リクエストURL**: `https://api.notion.com/v1/pages`
- **Header**:
  ```json
  {"Notion-Version":"2022-02-22"}
  ```
- **Body**:
  ```json
  {
    "parent": { "database_id": "<database_id>" },
    "properties": {
      "名前": {
        "title": [ { "text": { "content": "{一括処理対象の一要素}" } } ]
      },
      "タグ": {
        "select": { "name": "次にやる" }
      }
    }
  }
  ```
※bodyの細かい設定は以下を参照
https://developers.notion.com/reference/property-object
- 上記ボディを入力すると、上の方にdatabaseIDの入力欄が出現するので先ほどメモしたデータベースIDを入力する

---

## **5. 一括処理対象の入力**
💡ここがポイントです。一括処理対象の入力欄に、AIノードで実行した結果をコンテキストにしたいのですが、この時点ではコンテキストを何も入力できない状態です。

AIノードの出力を直接一括処理に渡すことはできないため、以下のステップが必要です:
1. AIノードのタスクのみを実行
2. 処理結果が配列として出力されることを確認する
3. 一括処理対象の入力欄に「/」と入れるとが入力できる様になる
4. Bodyの`content`「{一括処理対象の一要素}」を削除して、「/」を入れると、一括処理対象の要素をコンテキストとして入力できるようになる
※2024年12月11日時点の情報なので今後のアップデートで変更する可能性あり。
---

## **6. 実際の運用例**

実際に動作している様子は以下の動画をご覧ください。
[動画リンク: Xの投稿URL]

---

## **7. まとめ**

このスキルは、複数タスクを追加するようなシンプルなスキルですが、この一括処理を応用することで様々な業務に活用できると思います。特に多忙なビジネスパーソンにとって、手作業から解放される効果は絶大です。もし、他の活用事例があればXで共有してください！！

---
