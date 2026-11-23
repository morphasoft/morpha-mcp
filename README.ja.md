# Morpha MCP

**Morpha** は、AI アプリから [MCP（Model Context Protocol）](https://modelcontextprotocol.io) で動かせる、Windows 向けのパラメトリック 3D CAD です。
言葉で頼むと、AI が Morpha を操作して、モデルを作り、直し、確かめます。AI が使うのは、画面と同じ操作です。

- ホームページ: https://morphasoft.com/
- Beta 版のダウンロード（2027年3月31日まで無料。業務でも使えます）: https://morphasoft.com/beta/
- English: [README.md](README.md)

> このリポジトリは、AI アプリと Morpha をつなぐための説明を置く場所です。Morpha 本体は市販のデスクトップアプリで、オープンソースではありません。MCP のサーバー（`morpha-mcp.exe`）は、Morpha と一緒にインストールされます。

## 動いている様子

Morpha（左）と Morpha Agent（右。開発中）の画面の録画です。「干渉がないか確認して」と頼むと、AI が調べて、重なった所を赤く示します。

https://morphasoft.com/assets/video/agent-interference.mp4

## AI でできること

MCP のサーバーには、約 270 の操作があります。画面と同じ処理を呼ぶので、AI がしたことはフィーチャーの履歴に残り、人が元に戻す・編集する・直すことができます。

| 分野 | 例 |
| --- | --- |
| スケッチ | 直線・円弧・スプライン、寸法と幾何拘束 |
| フィーチャー | 押し出し・回転・スイープ・ロフト、穴とねじ、フィレット、面取り、シェル、ドラフト、パターン、ブーリアン |
| サーフェス・板金 | オフセット・トリム・ブレンド、フランジ・曲げ・展開 |
| アセンブリ | 部品の配置、拘束、状態、分解図、干渉チェック（結果を 3D ビューに表示） |
| 図面 | JIS に沿ったシート、投影図・断面図、寸法、PDF・DXF への書き出し |
| 確認 | 質量の情報、計測、3D ビューの画像（AI が結果を見て確かめる） |
| ファイル | Morpha のファイルを開く・保存する、STEP・IGES・STL・DXF の読み込みと書き出し |
| パラメーター | 名前の付いたパラメーターと式で、フィーチャーの値を動かす |

有料の機能（CAE・組立アニメーション。Pro のプラン。開発中）の操作は、そのライセンスがあるときだけ出ます。Beta 版には入っていません。

## 必要なもの

- Windows 10 または 11（64ビット）。Direct3D 11 に対応したグラフィックス
- Morpha をインストールして、起動していること
- MCP のサーバー（stdio）に対応した AI アプリ（例：Claude Desktop、Claude Code）

## つなぎ方

はじめに、Morpha の **設定 →「外部連携」** で、**「外部連携（API）を有効にする」** をオンにします（最初はオフです）。AI アプリから使う間は、Morpha を起動したままにします。

AI アプリに登録するのは、Morpha と一緒に入る `morpha-mcp.exe` です。場所は同じ設定の画面に出ます（標準では `%LocalAppData%\MorphaCAD\current\morpha-mcp.exe`）。

### Claude Desktop：ファイルを入れる（かんたん）

1. [最新のリリース](https://github.com/morphasoft/morpha-mcp/releases/latest)から `morpha-mcp-<版>.mcpb` をダウンロードします。
2. Claude Desktop の **「設定」→「拡張機能」** を開いて、そのファイルを入れます。
3. `morpha-mcp.exe` の場所の欄は、空のままにします。Morpha を別の場所に入れたときだけ、場所を入れます。

このファイルに MCP サーバーは入っていません。Morpha と一緒に入っている `morpha-mcp.exe` を起動するので、いつもお使いの Morpha と同じ版になります。

### ほかの AI アプリ：場所を登録する

`morpha-mcp.exe` を、**コマンド**として動かす MCP サーバー（stdio）として登録します。URL（アドレス）を入れる欄には登録できません。

| AI アプリ | 書く場所 | 書く内容 |
| --- | --- | --- |
| Claude Desktop（手で書く） | 「設定」→「開発者」→「構成を編集」（`claude_desktop_config.json`） | 下の `mcpServers` |
| Cursor | `~/.cursor/mcp.json`（全部のプロジェクト）か `.cursor/mcp.json`（1つのプロジェクト） | 下の `mcpServers` |
| Claude Code | ターミナル | `claude mcp add morpha -s user -- "C:\…\morpha-mcp.exe"` |
| VS Code | コマンドパレット →「MCP: Open User Configuration」（`mcp.json`） | 下の `servers` |

```json
{
  "mcpServers": {
    "morpha": { "command": "C:\\…\\morpha-mcp.exe" }
  }
}
```

VS Code は、名前が `servers` で、種類（`type`）も書きます。

```json
{
  "servers": {
    "morpha": { "type": "stdio", "command": "C:\\…\\morpha-mcp.exe" }
  }
}
```

- JSON のファイルでは、場所の `\` を `\\` と2つ重ねて書きます。
- Claude Code では、`-s user` を付けると、どのフォルダでも使えます。付けないと、コマンドを打ったフォルダだけに登録されます。
- 登録したら、AI アプリを起動し直します。

上の書き方は、2026年11月23日に各アプリの公式の説明で確かめました。設定の形はアプリの更新で変わることがあります（VS Code は新しい形に移っている途中です）。違っていたら、アプリの説明に従ってください。メニューの日本語の名前は、アプリの版で違うことがあります。

つながったら、たとえば「100×60mm、厚さ 8mm の板の四隅に、M6 のねじ穴をあけて」と頼んでみてください。

### つながらないとき

1. Morpha は起動していますか。「外部連携（API）を有効にする」はオンですか。
2. 登録した後に、AI アプリを起動し直しましたか。
3. `morpha-mcp.exe` を、URL ではなくコマンドとして登録していますか。
4. 場所は、Morpha の設定の画面に出ているものと同じですか。JSON のファイルでは、`\` を `\\` と書いていますか。
5. それでもつながらないときは、AI アプリの名前とエラーの文を添えて、[issues](https://github.com/morphasoft/morpha-mcp/issues) でお知らせください。

## データはどこへ送られるか

- Morpha は、お使いの PC の中からの接続だけを受け付けます（127.0.0.1、トークンが必要）。外部連携は、設定でオンにするまで切れています。
- Morpha のモデル・図面のファイルを、当方のサーバーへ送ることはありません。
- クラウドの AI アプリを使うと、頼んだ言葉と、AI が読んだモデルの情報（寸法・画像など）は、その AI アプリの提供元へ送られます。社外に出せない設計では、ご注意ください。
- **Morpha Agent**（開発中。無料）は、PC の中だけで動く自社の AI アプリです。データを社外に出しません。

## Python

同じ操作を Python（Morpha と一緒に入る `morpha` パッケージ）からも呼べるので、決まった作業をスクリプトにまとめられます。画面の操作を Python のマクロとして記録することもできます。

## 状態

Morpha は Beta 版です。不具合や足りない機能があり、API は版によって変わることがあります（0.x）。AI がうまく作れないこともあるので、結果は確かめてからお使いください。

## ご意見・不具合の報告

support@morphasoft.com へのメールか、このリポジトリの Issue でお知らせください。

## ライセンス

このリポジトリの説明の著作権は、Morpha の運営者にあります。Morpha と `morpha-mcp.exe` は、[Morpha 利用規約](https://morphasoft.com/legal/terms/)に従って配布しています。
