
# Claude Code ワークショップ

目的：Claude Codeのセットアップと最低限の操作知識を身につける

## インストール手順

Reference: https://code.claude.com/docs/en/setup

```bash
# npmから追加する場合

# バージョンの確認
node --version
v22.13.1

npm --version
10.9.2

# インストール
npm install -g @anthropic-ai/claude-code

# インストールされているか確認
claude --version
# 2.x.x (Claude Code)

# ネイティブインストールする場合

# Homebrew(Mac)
brew install --cask claude-code

# Mac/Linux/WSL
curl -fsSL https://claude.ai/install.sh | bash

# PowerShell(Windows)
irm https://claude.ai/install.ps1 | iex

# インストールされているか確認
claude --version
# 2.x.x (Claude Code)
```

## Claude Code 初回セットアップ（CLIベース）

```bash
# Claude Codeを起動
claude

# 初回起動時にテーマやサブスクリプションプランの設定を各自行う
# 以下のような選択画面が出るのでそれぞれ選択

#  Select login method:
#  ❯ 1. Claude account with subscription
#       Starting at $20/mo for Pro, $100/mo for Max - Best value, predictable pricing
#    2. Anthropic Console account
#       API usage billing

# Pro/MAXプランの場合は、1を選択し流れに沿って設定(契約済みなら特別な操作はほぼなし)

# Bedrockの場合は、2を選択し以下の手順に沿って設定
# https://code.claude.com/docs/en/amazon-bedrock
```

## 読み取り専用/編集用モードの切り替え

AIコーディングエージェントには、大体2つのモードがあります。

1つ目は、ファイル編集やコマンドの実行を禁止して読み取りのみを実行する読み取り専用のモード（plan mode）
2つ目は、ファイル編集やコマンドの実行を許可するモード（auto-accept edits on）

「shift+tab」でそれぞれのモードが切り替えられます。

AIコーディングエージェントでは、Plan-Then-Executeというプロセスが推奨されます。

まずは`plan mode`で実装の計画を立て、内容を確認します。人間が内容を確認して問題なければ`auto-accept edits`に切り替えて実行してもらいます。

### ワーク0：Plan-Then-Executeを試す

- plan modeに切り替えて、以下のプロンプトで計画の立案だけさせてください
  - 「コッペ田島に関するランディングページを作成してください」

## セッションの切り替え

claudeは、毎回端末から立ち上げると新しいセッションが作成されます。セッション内で人からの入力やファイルの調査内容がメモリに記録されます。
セッションが切り替わると、上記のメモリに置いた情報は継続されません。

もしClaude Codeを再起動してメモリを継続したい場合は、以下のコマンドで実行できます。

```bash
# 直近のセッションを使用して実行
claude -c

# 以前のセッションリストから選択して実行
claude -r
```

### ワーク1: セッションの切り替え

- Ctrl+Cを2回押してClaude Codeを停止し、上記のコマンドで再度同じセッションを開いてください

## メモリの構築

Reference: https://code.claude.com/docs/en/memory

Claude Codeではセッションを切り替えると、調査内容やファイル情報が毎度削除されます。AIコーディングエージェントは、コンテキストの範囲内でしか動作しないため、毎回プロジェクトの情報を再調査する必要があり、時間とトークンを消費します。この問題を解決するために、Claude Codeではメモリ機能を使用できます。

### メモリの種類

Claude Codeには以下のメモリ機能があります（よく使うものだけ記載）

| メモリタイプ | ファイルパス | スコープ | 用途 |
|------------|-------------|---------|------|
| Project memory（プロジェクトメモリ） | `./CLAUDE.md` または `./.claude/CLAUDE.md` | チーム共有のプロジェクト用指示 | プロジェクトアーキテクチャ、コーディング規約、共通ワークフロー |
| User memory（ユーザーメモリ） | `~/.claude/CLAUDE.md` | 全プロジェクト共通の個人設定 | コードスタイルの好み、個人用ツールのショートカット |

既存のPJで開始する場合は、Claude Code上で`/init`と実行することでPJの情報を読み取ってメモリを作成してくれます。
以下はメモリの例です。メモリファイルは1つではなく`@`と書くことで別のファイルを関連して読み込みもできます。

```md
See @README for project overview and @package.json for available npm commands for this project.

# Additional Instructions
- git workflow @docs/git-instructions.md
```

`/memory`コマンドで、現在のメモリがセッション内で読み込まれているのか確認や、編集ができます。

### ワーク2：メモリを試す

- 既存のPJがある場合は、`/init`でソースコードを解析してメモリを作成してください
- メモリを複数呼び出すため、@でほかのファイルを指定してください

## よく使うショートカット

- `@`でファイルをコンテキストに追加
- `!`でClaude Code上でLinuxコマンドなどを実行
- `/`でビルドインのスラッシュコマンドが実行可能。`/model`でのモデル確認や`/review`によるコードレビューなどが可能

Reference: https://code.claude.com/docs/ja/slash-commands

## 権限制御（Permission）

Reference: https://code.claude.com/docs/en/settings#permissions

Claude Codeは、権限を与えるとその権限範囲で自由に行動します。PJ外のファイル操作や想定していないコマンドの実行を防ぐためコンテナやVMなど実行環境を制限したり、Claude Code外で持ちうる権限を制限する必要があります。
Claude Code内でも権限制御が可能なPermissionがあるので合わせて使います。

- `.claude/settings.json`：PJ内で共通したい権限制御
- `.claude/settings.local.json`：個人で設定する権限制御

Claude Codeを特殊なオプションなしで起動している場合は、ファイルやプロセスなどに変更が入るコマンドやインターネットアクセスは実行単位で確認が発生します。常時許可して良い処理は`Allow`で許可すると確認をスキップできます。逆に禁止したい処理は`Deny`で止めておくことができます。

```json:.claude/settings.json
{
  "permissions": {
    "allow": [
      "Bash(git diff:*)",
      "Edit(docs/**)",
      "Read(~/.zshrc)",
      "WebFetch(domain:example.com)"
    ],
    "deny": [
      "Read(./.env)",
      "Bash(rm -rf:*)",
      "Bash(sudo :*)",
      "Bash(su - :*)"
    ]
  }
}
```

またMCPの権限もこのファイルで許可されます。こちらも同様にMCPのツールを実行時にユーザー側へ許可するか聞かれます。

### Permissionの実際の使い方

最初からすべてを許可するか禁止するか設定は難しいです。最低限禁止コマンドを設定し、許可するコマンドは対話的にClaude Codeから自動で設定できます。
Claude Codeからコマンド実行、ネットワーク接続などの確認の際に3択で質問されます。1は今回だけ許可、2はこのコマンドの類似したコマンドは許可、3は拒否になります。2を選択すると`.claude/settings.local.json`に自動で追記されます。自動追記された内容を確認しながら徐々に範囲を広げてください。

もしコンテナやVMなどの環境が整う際は、`claude --dangerously-skip-permissions`でClaude Codeを起動することで、ユーザーの許可をバイパスしてコマンドなどをClaude Codeが自動実行します。`Deny`に入れているコマンドは実行できないので最低限の防衛は実行してください。

### ワーク3：禁止コマンドを試す

- Deny設定で禁止するコマンド(例えばlsなど)を設定してください。Claude Codeで禁止したコマンドを実行するよう指示してください
- Deny設定で特定のドメインへのWebFetchを設定し、実行されないことを確認してください

## カスタムスラッシュコマンド

Reference: https://code.claude.com/docs/ja/slash-commands#カスタムスラッシュコマンド

ここまで`/init`や`/memory`などスラッシュコマンドが登場しました。この機能はClaude Codeに特定のプロンプトを読み込ませて実行されています。このスラッシュコマンドをユーザー側で独自に作成することもできます。`.claude/commands/`配下に`〇〇.md`ファイルを作ると作成できます。

たとえば、以下のようなgitコミットのメッセージを考えてコミットさせるカスタムスラッシュコマンドを作れます。

```md:.claude/commands/create-commit.md
- 現在の内容をgitにコミット
- コミットメッセージはConventional Commit形式で作成
```

上記のコマンドはClaude Code上で以下のように呼び出すことでClaude Code上で使えます。Claude Codeを再起動しないと反映されない場合があるので、実行できない場合はClaude Codeを停止し、`claude -c`などで再起動してください。

```bash
claude -c

/create-commit.md
```

### ワーク4：カスタムスラッシュコマンドを作成

- 先程紹介したコマンドを作成し、Claude Code上で実行してください
- 何らかの定型作業をカスタムスラッシュコマンド化してください

## AI駆動開発ワークショップ

よりシステム開発に近い内容を操作して確認していきます。

### ワーク5：メモリの効果を確認

- プロンプトでライブラリやフレームワークを指定する場合と指定しない場合を比較してください。例が思いつかない場合は以下のプロンプトを使ってください
  plan modeで計画を立てて、実装させてください
  - ない場合：「おみくじアプリを作ってください」
  - ある場合：「おみくじアプリを作ってください。実装にはShell Scriptを使ってCLI上で実行してください。」

### ワーク6：TODOアプリの作成

- 以下のプロンプトでシンプルなTODOアプリを作成してください。plan modeで計画して、実装に切り替えてください
  - 「TypeScriptでTODOアプリを作成してください。」

### ワーク7：AIによるセキュリティチェック

- 生成したコード上で`/security-review`コマンドを実行し、セキュリティ面の問題がどこにあるか確認してください

### ワーク8：MCPの活用

- Claude Codeに頼んで、Chrome DevTools MCPを導入してください
- メモリに以下の文言を追加してください
  - 「- フロントエンドの実装を修正した後、Chrome DevTools MCPで動作を検証してください」
- フロントエンドの修正が必要になるようなタスクを考えてClaude Codeに依頼してください

