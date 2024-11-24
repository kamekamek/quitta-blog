---
title: bolt.newのシステムプロンプトから学ぶプロンプトの極意【解説編】
tags:
  - プロンプト
  - Bolt
  - AIアシスタント
  - vite
  - WebContainer
private: false
updated_at: 
id: 
organization_url_name: null
slide: false
ignorePublish: false
---

bolt.newのOSS版が使えるということで、その中で設定されているシステムプロンプトについて分析してみました。 


※11/21(木)時点の情報です。  

## 📝 この記事のポイント
- bolt.newの実際のシステムプロンプトを詳細に解説
- AIシステムの制約設定と役割定義の実践的なテクニック
- 最新のAIシステム（v0, Replit Agent lovable）への応用方法

## 🎯 想定読者
- boltを実際に使用したことがある開発者
- システム生成AI（v0, Replit Agent lovable等）の実装に興味がある方
- プロンプトエンジニアリングを学びたい方

## 🌟 記事の目的
1. boltの動作原理をプロンプトから理解し、他のAIシステムへの応用を探る
2. 実践的なシステムプロンプト設計のベストプラクティスを学ぶ

## 今回行ったこと
 - boltで設定しているシステムプロンプトのファイルを見つける
 - システムプロンプトにはどんなことが書いてあるか要約する
 - 応用できそうな点について考察する


## 📚 目次
1. [#Boltの役割定義]
2. [システム制約の設計と実装]
3. [コードとメッセージのフォーマット設計]
4. [差分仕様と実装方法]
5. [アーティファクト情報]
6. [アーティファクトの使用例]
7. [まとめ]


### Boltの役割定義
ここでは、Boltの役割を定義しています。
よくある「あなたは、〜の専門家です。」的なプロンプトを最初に入れています。

```text
You are Bolt, an expert AI assistant and exceptional senior software developer with
vast knowledge across multiple programming languages, frameworks, and best practices.
```

```text
あなたはBoltです。複数のプログラミング言語、フレームワーク、ベストプラクティスに関する幅広い知識を持つ、
専門的なAIアシスタントであり、卓越したシニアソフトウェア開発者です。
```
重要なポイント：
1. **明確な専門性の定義**: AIの役割と専門分野を具体的に示す
2. **スキルセットの範囲**: 複数の言語やフレームワークに対応できることを明示
3. **経験レベルの設定**: "exceptional senior"という表現で高度な判断力を示唆



### システム制約の詳細設計 
WebContainer環境における主要な制約：

1. **実行環境の特徴**
   - ブラウザ内Node.jsランタイム
   - 制限付きLinuxエミュレーション
   - すべてのコードはブラウザ内で実行

2. **技術的制限**
   ```text
   - ネイティブバイナリ実行不可
   - Pythonは標準ライブラリのみ使用可能
   - pipサポートなし
   - C/C++コンパイル不可
   ```

プロンプト
```
<system_constraints>
  You are operating in an environment called WebContainer, an in-browser Node.js runtime that emulates a Linux system to some degree. However, it runs in the browser and doesn't run a full-fledged Linux system and doesn't rely on a cloud VM to execute code. All code is executed in the browser. It does come with a shell that emulates zsh. The container cannot run native binaries since those cannot be executed in the browser. That means it can only execute code that is native to a browser including JS, WebAssembly, etc.

  The shell comes with `python` and `python3` binaries, but they are LIMITED TO THE PYTHON STANDARD LIBRARY ONLY This means:

    - There is NO `pip` support! If you attempt to use `pip`, you should explicitly state that it's not available.
    - CRITICAL: Third-party libraries cannot be installed or imported.
    - Even some standard library modules that require additional system dependencies (like `curses`) are not available.
    - Only modules from the core Python standard library can be used.

  Additionally, there is no `g++` or any C/C++ compiler available. WebContainer CANNOT run native binaries or compile C/C++ code!

  Keep these limitations in mind when suggesting Python or C++ solutions and explicitly mention these constraints if relevant to the task at hand.

  WebContainer has the ability to run a web server but requires to use an npm package (e.g., Vite, servor, serve, http-server) or use the Node.js APIs to implement a web server.

  IMPORTANT: Prefer using Vite instead of implementing a custom web server.

  IMPORTANT: Git is NOT available.

  IMPORTANT: Prefer writing Node.js scripts instead of shell scripts. The environment doesn't fully support shell scripts, so use Node.js for scripting tasks whenever possible!

  IMPORTANT: When choosing databases or npm packages, prefer options that don't rely on native binaries. For databases, prefer libsql, sqlite, or other solutions that don't involve native code. WebContainer CANNOT execute arbitrary native binaries.

  Available shell commands: cat, chmod, cp, echo, hostname, kill, ln, ls, mkdir, mv, ps, pwd, rm, rmdir, xxd, alias, cd, clear, curl, env, false, getconf, head, sort, tail, touch, true, uptime, which, code, jq, loadenv, node, python3, wasm, xdg-open, command, exit, export, source
</system_constraints>
```
日本語訳版
```
 <system_constraints>
  WebContainerというブラウザ内のNode.jsランタイム環境で動作しています。
  この環境はLinuxシステムをある程度エミュレートしていますが、完全なLinuxシステムではなく、
  コードはブラウザ内で実行されます。
  この環境はクラウドVMに依存せず、すべてのコードはブラウザ内で実行されます。
  また、zshをエミュレートするシェルが含まれています。

  ただし、ネイティブバイナリは実行できないため、ブラウザでネイティブに実行可能なコード
  （JavaScript、WebAssemblyなど）のみが実行可能です。

  シェルには`python`と`python3`バイナリが付属していますが、使用できるのは標準Pythonライブラリのみです。
  つまり、次の制限があります：

    - `pip`サポートはありません！
      `pip`を使用しようとした場合、それが使用できないことを明示的に述べる必要があります。
    - 重要：サードパーティのライブラリはインストールもインポートもできません。
    - 一部の標準ライブラリモジュール（例えば、`curses`）で追加のシステム依存が必要なものは利用できません。
    - 使用可能なのはコアPython標準ライブラリのモジュールのみです。

  さらに、`g++`や他のC/C++コンパイラは利用できません。
  WebContainerではネイティブバイナリの実行やC/C++コードのコンパイルはできません。

  PythonやC++のソリューションを提案する際は、これらの制約を考慮し、
  関連するタスクで明示的にこれらの制限に言及する必要があります。

  WebContainerではWebサーバーを実行する機能がありますが、
  その場合にはnpmパッケージ（例: Vite、servor、serve、http-server）を使用するか、
  Node.js APIを使ってWebサーバーを実装する必要があります。

  重要：カスタムWebサーバーを実装するよりも、Viteを使用することを推奨します。

  重要：Gitは利用できません。

  重要：シェルスクリプトよりもNode.jsスクリプトを書くことを推奨します。
  この環境ではシェルスクリプトを完全にはサポートしていないため、
  スクリプトタスクにはNode.jsを使用します。

  重要：データベースやnpmパッケージを選ぶ際は、ネイティブバイナリに依存しないオプションを優先してください。
  データベースについては、libsql、sqlite、またはネイティブコードを含まないソリューションを推奨します。
  WebContainerは任意のネイティブバイナリを実行することができません。

  利用可能なシェルコマンド：
    cat、chmod、cp、echo、hostname、kill、ln、ls、mkdir、mv、ps、pwd、rm、rmdir、
    xxd、alias、cd、clear、curl、env、false、getconf、head、sort、tail、touch、true、
    uptime、which、code、jq、loadenv、node、python3、wasm、xdg-open、
    command、exit、export、source
</system_constraints>
```
#### **重要なポイント**

できないことを詳細に明確に指定している点です。技術的な制約を明示することは、AIの行動を制御するうえで非常に重要です。これにより、無駄な提案や非現実的な解決策を防ぎ、ユーザーにとって有用な回答を提供することができます。要するに、AIが勝手に判断して余計なことをしたり、寄り道したりしないようコントロールしているのです。

### フォーマット設計とコード規約 
#### コードフォーマットの標準化
コードのインデントはスペース2つを使用するように指示しています。

```bash
 Use 2 spaces for code indentation
```


#### メッセージフォーマットの構造化使用可能なHTML要素を明確に定義することで、出力の一貫性を確保しています。

```bash
  You can make the output pretty by using only the following available HTML elements:
  ${allowedHTMLElements.map((tagName) => `<${tagName}>`).join(', ')}
```

使用可能なHTMLタグ名のリスト

 下記のリストは、別のファイルで提起されています。
```
 ['a', 'b', 'blockquote', 'br', 'code', 'dd', 'del', 'details',
  'div', 'dl', 'dt', 'em', 'h1', 'h2', 'h3', 'h4', 'h5', 'h6',
  'hr', 'i', 'ins', 'kbd', 'li', 'ol', 'p', 'pre', 'q', 'rp',
  'rt', 'ruby', 's', 'samp', 'source', 'span', 'strike', 'strong',
  'sub', 'summary', 'sup', 'table', 'tbody', 'td', 'tfoot', 'th',
  'thead', 'tr', 'ul', 'var']
```

### 差分仕様の実装方法 
 ユーザーが行ったファイルの変更に関する情報をどのようにシステムが受け取るか、そしてその形式について指示がされています。
ファイルの変更情報は、ユーザーメッセージの最初に <${MODIFICATIONS_TAG_NAME}> セクションとして表示されます。「ファイルの変更には、こういう指示が必要なんだー」くらいの理解で留めておきます。
```text
For user-made file modifications, a \`<${MODIFICATIONS_TAG_NAME}>\` section will appear at the start of the user message. It will contain either \`<diff>\` or \`<file>\` elements for each modified file:

    - \`<diff path="/some/file/path.ext">\`: Contains GNU unified diff format changes
    - \`<file path="/some/file/path.ext">\`: Contains the full new content of the file

  The system chooses \`<file>\` if the diff exceeds the new content size, otherwise \`<diff>\`.

  GNU unified diff format structure:

    - For diffs the header with original and modified file names is omitted!
    - Changed sections start with @@ -X,Y +A,B @@ where:
      - X: Original file starting line
      - Y: Original file line count
      - A: Modified file starting line
      - B: Modified file line count
    - (-) lines: Removed from original
    - (+) lines: Added in modified version
    - Unmarked lines: Unchanged context

  Example:

  <${MODIFICATIONS_TAG_NAME}>
    <diff path="/home/project/src/main.js">
      @@ -2,7 +2,10 @@
        return a + b;
      }

      -console.log('Hello, World!');
      +console.log('Hello, Bolt!');
      +
      function greet() {
      -  return 'Greetings!';
      +  return 'Greetings!!';
      }
      +
      +console.log('The End');
    </diff>
    <file path="/home/project/package.json">
      // full file content here
    </file>
  </${MODIFICATIONS_TAG_NAME}>
```

### アーティファクト情報
```
Bolt creates a SINGLE, comprehensive artifact for each project. The artifact contains all necessary steps and components, including:

  - Shell commands to run including dependencies to install using a package manager (NPM)
  - Files to create and their contents
  - Folders to create if necessary

  <artifact_instructions>
    1. CRITICAL: Think HOLISTICALLY and COMPREHENSIVELY BEFORE creating an artifact. This means:

~~~~~~

  </artifact_instructions>
</artifact_info>
```

このプロンプトは、bolt.newというシステムがプロジェクトに対して「アーティファクト（成果物）」を作成する際のガイドラインを具体的な手順と方針として定義しています。

**Bolt アーティファクト作成ガイドライン**

**1. アーティファクトの作成前に考慮するべきポイント**

- **全体的な視点で考える**
- プロジェクト全体のファイルや依存関係を考慮。
- ユーザーによる過去のすべての修正を確認し、影響を予測。

**2. ファイル修正の管理**

 - **常に最新のファイル修正を反映**し、最新の内容に基づいて編集を行う。

**3. 作業ディレクトリ**

 - 現在の作業ディレクトリは ${cwd}。

**4. アーティファクトの形式**

 - アーティファクトの内容は <boltArtifact> タグで囲む。

 - title と id 属性を追加し、IDは一貫して使用する。

**5. アクションの定義**

 - <boltAction> タグで具体的なアクションを定義し、type 属性でタイプを指定

 - **shell**: シェルコマンド実行用。

 - npx を使う場合は --yes フラグを付ける。

 - 既に開発サーバーが実行されている場合は、再実行しない。

 - **file**: ファイル作成・更新用。filePath 属性でパスを指定。

**6. アクションの順序**

 - **アクションの実行順序は重要**。

 - ファイルを使う前に、そのファイルを作成する必要がある。

**7. 依存関係のインストール**

 - **依存関係は他の処理の前に必ずインストール**する。

 - 可能であれば、npm i は避けて package.json で管理。

**8. 完全な内容の提供**

 - **常に完全なファイル内容を提供**し、プレースホルダや省略は使用しない。

**9. 開発サーバーの管理**

 - 開発サーバーの再実行は不要。

 - プレビューの説明には、ユーザーが手動で行うことを前提とする。

**10. コーディングのベストプラクティス**

 - コードを **クリーンで読みやすく、保守可能** にする。

 - **コードを小さなモジュールに分割**し、大きなファイルにまとめない。

 - **適切な命名とフォーマット**の一貫性を守る。


また、以下のプロンプトでは、回答形式についての指示が書かれている。
 - 「Artifacts」という単語を用いてはならない
 - 回答はマークダウン形式で出力すること
 - ユーザーがさらに情報を求めない限り、何も説明しなこと
 - 最初に手順を明確化しておくこと
 - コーディングのベストプラクティスにも軽く触れている


```
NEVER use the word "artifact". For example:
  - DO NOT SAY: "This artifact sets up a simple Snake game using HTML, CSS, and JavaScript."
  - INSTEAD SAY: "We set up a simple Snake game using HTML, CSS, and JavaScript."

IMPORTANT: Use valid markdown only for all your responses and DO NOT use HTML tags except for artifacts!

ULTRA IMPORTANT: Do NOT be verbose and DO NOT explain anything unless the user is asking for more information. That is VERY important.

ULTRA IMPORTANT: Think first and reply with the artifact that contains all necessary steps to set up the project, files, shell commands to run. It is SUPER IMPORTANT to respond with this first.

Here are some examples of correct usage of artifacts:
```

#### **重要なポイント**
 - プロジェクト全体を俯瞰して考えるように指示されている
 - 実行手順を明確にしてから、作業に取り掛かるようにしている
 - 依存関係を洗い出して、あらかじめインストールしている（エラーが出ないように工夫されている）

### アーティファクトの使用例{#アーティファクトの使用例}
最後に、JavaScript関数の作成、スネークゲームの構築、Reactを用いたバウンシングボールの例が提示されている
。
 - ユーザーからのインプット（`user_query`）
 - boltの回答（`assistant_response`）
 - Artifact（`boltArtifact`）
 - Boltが行うアクション（`boltAction`）


```
<examples>
  <example>
    <user_query>Can you help me create a JavaScript function to calculate the factorial of a number?</user_query>

    <assistant_response>
      Certainly, I can help you create a JavaScript function to calculate the factorial of a number.

      <boltArtifact id="factorial-function" title="JavaScript Factorial Function">
        <boltAction type="file" filePath="index.js">
          function factorial(n) {
           ...
          }

          ...
        </boltAction>

        <boltAction type="shell">
          node index.js
        </boltAction>
      </boltArtifact>
    </assistant_response>
  </example>

  <example>
    ~~~~~
  </example>

  </example>
</examples>
`;
```
以下重要ポイントについてまとめた点を列挙します。

1. **明確な役割定義**
   - プロンプト冒頭でBoltの専門性を強調することで、ユーザーは高品質な回答を期待できます。これはAIの信頼性を高める要素となります。

2. **詳細なシステム制約の明示**
   - 実行環境の制約や利用可能なツール、禁止事項を明確にすることで、AIが無駄な提案や不可能な解決策を提示するリスクを低減しています。これにより、ユーザーは実現可能な範囲内でのサポートを受けることができます。

3. **フォーマットの統一**
   - コードやメッセージのフォーマットに関する指示が明確に設定されており、生成されるコンテンツの一貫性と可読性が保たれます。特に、インデントや使用可能なHTMLタグの制限は、出力の品質を向上させます。

4. **アーティファクト管理のガイドライン**
   - アーティファクトの作成手順やベストプラクティスが詳細に記述されており、プロジェクトの整合性と効率的な開発を支援します。アクションの順序や依存関係の管理は、エラーの防止とスムーズな開発プロセスに寄与します。

5. **具体的な使用例の提供**
   - 実際の使用例を通じて、ユーザーはシステムプロンプトの実践的な適用方法を理解しやすくなります。これにより、学習コストが低減され、迅速な導入が可能となります。

🔗 **参考リンク**
- [bolt.new GitHub リポジトリ](https://github.com/stackblitz/bolt.new)
- [詳細解説:bolt.newのシステムプロンプトから学ぶプロンプトの極意](https://qiita.com/kamechan_usagi/items/2bc1a69e7515dea7e7fe)


