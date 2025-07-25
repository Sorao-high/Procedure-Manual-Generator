# インタラクティブ作業手順書ジェネレーター (Interactive Procedure Manual Generator)

これは、Webブラウザ上で動作する対話形式の作業手順書を簡単に作成するためのツールです。
コマンドの手動入力によるミスや、作業工程の抜け漏れといったヒューマンエラーを防ぎ、誰でも安全かつ正確に定型業務を遂行することを目指します。

![アプリケーションのスクリーンショット](https://i.imgur.com/your-screenshot-url.png)  
*(ここにアプリケーションのスクリーンショット画像を挿入することを推奨します)*

## ✨ 主な機能

*   **ウィザード形式のUI**: 作業をチャンク（塊）単位で一つずつ表示し、利用者を迷わせません。
*   **動的な変数埋め込み**: 顧客IDやホスト名など、作業ごとに変わる値を事前にフォーム入力するだけで、必要なコマンドを自動生成します。
*   **進捗インジケーター**: 大項目単位で進捗が表示され、今どの段階の作業をしているかが一目でわかります。
*   **コピー機能**: 表示されたコマンドはワンクリックでクリップボードにコピーでき、ターミナルへの貼り付けミスを防ぎます。
*   **シングルファイル構成**: `html`ファイル1つで動作するため、Webサーバーは不要です。ローカル環境でそのまま利用できます。
*   **カスタマイズの容易さ**: HTML内のJavaScriptオブジェクトを書き換えるだけで、独自の作業手順書を簡単に作成できます。

## 🚀 使い方

1.  このリポジトリをクローン、またはZIP形式でダウンロードします。
2.  `procedure_wizard.html` ファイルをGoogle ChromeやFirefoxなどのモダンなWebブラウザで開きます。
3.  表示された画面の指示に従って作業を進めてください。

特別なインストール作業やビルドは一切不要です。

## 🔧 手順書のカスタマイズ方法

独自の手順書を作成するには、`procedure_wizard.html` ファイルをテキストエディタで開き、`<script>`タグ内にある`procedureData`というJavaScriptオブジェクトを編集します。

### 1. 基本構造

`procedureData`オブジェクトは、手順書全体の構造を定義しています。

```javascript
const procedureData = {
    title: "Webサーバー設定作業手順", // 手順書全体のタイトル
    chapters: [ // 章（大項目）の配列
        {
            title: "初期設定", // 章のタイトル
            chunks: [ // チャンク（中項目）の配列
                {
                    title: "1.1. ユーザーアカウント作成", // チャンクのタイトル
                    tasks: [ // 具体的な作業の配列
                        { 
                            instruction: "作業用ユーザー `webmaster` を作成します。", // 作業指示
                            command: "useradd webmaster" // 実行するコマンド
                        },
                        // ... 他の作業 ...
                    ]
                },
                // ... 他のチャンク ...
            ]
        },
        // ... 他の章 ...
    ]
};
```

### 2. データ構造の詳細

*   `title` (文字列): 手順書全体のタイトルです。現在の実装では表示されませんが、将来的な拡張のために定義されています。
*   `chapters` (配列): 手順書の大項目となる「章」のリストです。
    *   `title` (文字列): 章のタイトルです。画面上部の**進捗インジケーター**に表示されます。
    *   `chunks` (配列): 章をさらに細分化した「チャンク」のリストです。ウィザード形式の1ステップが1チャンクに相当します。
        *   `title` (文字列): チャンクのタイトルです。作業画面で `<h3>` タグとして表示されます。
        *   `tasks` (配列): チャンク内で行う具体的な作業のリストです。
            *   `instruction` (文字列): 利用者への作業指示内容です。チェックボックスの横に表示されます。
            *   `command` (文字列): 実行するコマンドです。黒いコードブロック内に表示されます。

### 3. 変数の使い方

コマンド内で可変の値（顧客IDなど）を使いたい場合、`{{変数名}}`という形式でプレースホルダーを埋め込みます。

**例:**
```javascript
{
    instruction: "顧客ごとのドキュメントルートディレクトリを作成します。",
    command: "sudo mkdir -p /var/www/html/{{customerId}}" // customerIdが変数
}
```

この`{{customerId}}`は、HTMLの入力フォームと連動しています。
HTML側で、対応する`id`を持つ入力エリアを定義する必要があります。

```html
<div class="variable-group">
    <label for="customerId">顧客ID (customerId)</label>
    <div id="customerId-inputs"> <!-- この "customerId" が変数名と対応 -->
        <input type="text" placeholder="例: customerA">
    </div>
    <button class="add-variable-btn" data-target="customerId-inputs">+追加</button>
</div>
```

#### 複数の変数を追加する方法

新しい種類の変数を追加したい場合は、HTML内の`<div class="variable-input-area">`セクションに、上記の`<div class="variable-group">...</div>`ブロックをコピー＆ペーストし、`id`や`label`を新しい変数名（例: `serverName`）に変更してください。

## 🤖 AIによる手順書作成の補助

このREADMEの「🔧 手順書のカスタマイズ方法」セクションの内容を理解した生成AI（ChatGPT, Geminiなど）は、新しい手順書の`procedureData`オブジェクトを生成する手助けができます。

以下のプロンプトをコピーして、AIに指示してみてください。

---

### プロンプト例

「あなたは、インタラクティブな作業手順書を作成するアシスタントです。これから私が作りたい手順書の内容を自然言語で伝えますので、提示された仕様に基づいて`procedureData`オブジェクトをJavaScript形式で生成してください。

**仕様:**
*   `procedureData`は`title`と`chapters`のキーを持つ。
*   `chapters`は`title`と`chunks`のキーを持つオブジェクトの配列。
*   `chunks`は`title`と`tasks`のキーを持つオブジェクトの配列。
*   `tasks`は`instruction`と`command`のキーを持つオブジェクトの配列。
*   コマンド内の変数は `{{変数名}}` の形式で記述する。

**作成したい手順書の内容:**
（ここに、新しく作成したい手順書の流れを箇条書きなどで記述する）

例:
- **章1: Dockerのインストール**
  - **チャンク1.1: 古いバージョンの削除**
    - 作業: 古いDockerパッケージをアンインストールする。
    - コマンド: `sudo apt-get remove docker docker-engine docker.io containerd runc`
  - **チャンク1.2: リポジトリのセットアップ**
    - 作業: `apt`パッケージインデックスを更新する。
    - コマンド: `sudo apt-get update`
    - 作業: 必要な証明書をインストールする。
    - コマンド: `sudo apt-get install ca-certificates curl gnupg`
- **章2: アプリケーションのデプロイ**
  - **チャンク2.1: {{appName}}用のディレクトリ作成**
    - 作業: アプリケーション`{{appName}}`用のディレクトリを作成する。
    - コマンド: `mkdir /opt/apps/{{appName}}`

上記の内容で`procedureData`オブジェクトを生成してください。」

