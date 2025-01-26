---
title: '[GitHub Actions] モノレポ環境で Swatinem/rust-cache@v2 が効かなくてハマった'
tags:
  - Rust
  - CI
  - monorepo
  - GitHubActions
  - rust-cache
private: false
updated_at: '2025-01-26T10:08:24+09:00'
id: cab8aa34c51726ac8bdb
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

Rust の CI ビルドを高速化する方法として、依存ライブラリやビルド成果物のキャッシュを使う選択肢がよく挙げられます。
GitHub Actions で Rust プロジェクトをビルドしている場合、`Swatinem/rust-cache` アクションを導入するといい感じに `~/.cargo` と `~/.target` をキャッシュしてくれるので多くの人が利用していると思います。

https://github.com/Swatinem/rust-cache

先日、Rust をバックエンドに使ったモノレポ構成のプロジェクトに `Swatinem/rust-cache@v2` を導入してみたところ、キャッシュが効かずにあれ？ってなったので、そのときに調べた原因と解決策について共有しようと思います。

# 前提

## 環境

以下のようなモノレポ環境（プロジェクトのトップレベルに Rust プロジェクトが存在しない環境）を前提とします。

```bash
test-rust-cache-mono
├── .github
│   └── workflows
│       └── rust-ci.yml # <- rust用のCI
├── backend  # <- Rustプロジェクト
│   ├── Cargo.lock
│   ├── Cargo.toml
│   ├── src
│   └── target
└── frontend
```

## バージョン

- `Swatinem/rust-cache@v2` （2.7.7）
    - 今後のバージョンアップで挙動が変更される可能性があるためご注意ください

# 先に結論

以下の 🟢 OK の YAML のように、`Swatinem/rust-cache` の `workspaces` オプションで Rust プロジェクトのディレクトリを指定する必要がありました。

意外だったのは、以下のように `working-directory` を定義していてもダメだったことです。

```yaml: 🔴 NG（rust-ci.yml）
defaults:
  run:
    shell: bash
    working-directory: backend # <- これがrust-cacheアクションで効かない

jobs:
  check:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Cache dependencies
        uses: Swatinem/rust-cache@v2
```

```yaml: 🟢 OK（rust-ci.yml）
defaults:
  run:
    shell: bash
    working-directory: backend

jobs:
  check:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Cache dependencies
        uses: Swatinem/rust-cache@v2
        # これが必要
        with:
          workspaces: |
            backend -> target
```

# 起きた事象（`rust-cache` でエラー発生）

当初、前述した 🔴NG の YAML のように `workspaces` オプションを含めていませんでした。
この状態でリモートリポジトリに push し、実行された Github Actions のログを確認したところ、`Cache dependencies` ステップで以下のようなエラーが発生していました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/695294fe-59c3-bd15-ef79-7a3f40a04478.png)

どうやら、`rust-cache` アクション内部で呼び出される `cargo metadata` コマンドがエラーを出しているようです。

ちなみに、上のエラーが発生しても **`Cache dependencies` ステップ自体は成功してしまう**ので、ログを見ないとわからんという状態なので注意です…

また、後続の `Post Cache dependencies` ステップではキャッシュの保存は行われていない状態でした。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/9ca43ce4-f916-4443-3a91-54ee220d639b.png)

# `working-directory` を指定しているのになぜエラーになる？

🔴NG の YAML では、`defaults` 内で `working-directory: backend` と指定しているので、それ以外の処理はすべて backend 配下で正しく動いていました。
にもかかわらず、rust-cache のほうでは `Cargo.toml` が見つからないというメッセージが出ており、最初は「？？」となりました。

ログの内容をちゃんと見ると、

```
command: 'cargo metadata --all-features --format-version 1 --no-deps',
stderr: error: could not find `Cargo.toml` in `/home/runner/work/test-rust-cache-mono/test-rust-cache-mono` or any parent directory\n'
```

とあるので、どうやらトップレベルで `Cargo.toml` 探しているように見えます。

なので、runner で `working-directory: backend`としていても、rust-cache の内部ではトップレベルをカレントディレクトリとして `cargo metadata` を走らせているようでした。

## `Swatinem/rust-cache` ソースコードを見てみる

`Swatinem/rust-cache` の README をみると以下のように書いてあります。
```yml: README.mdの抜粋
    # The cargo workspaces and target directory configuration.
    # These entries are separated by newlines and have the form
    # `$workspace -> $target`. The `$target` part is treated as a directory
    # relative to the `$workspace` and defaults to "target" if not explicitly given.
    # default: ". -> target"
    workspaces: ""
```
デフォルトは `. -> target` になることがわかりましたが、README には以下が明記されておらず疑問が残りました。

- この `workspaces` が実際に `cargo metadata` のカレントディレクトリになるのか？
- GitHub Actions の `working-directory` は参照しないのか？

ということで、上記を確認するために `Swatinem/rust-cache` のソースコードを追ってみました。

TypeScript 製のアクションなので、`/src/` 以下を見てみます。
該当処理が書かれていたのは以下です。

- `/src/workspace.ts`
- `/src/config.ts`

### `/src/workspace.ts`
`Workspace` クラスの `getPackages` メソッドには以下のように `cargo metadata` を実行するディレクトリの設定があります。`{ cwd: this.root }` の部分ですね。

```ts:/src/workspace.ts
public async getPackages(filter: (p: Meta["packages"][0]) => boolean, ...extraArgs: string[]): Promise<Packages> {
  let packages: Packages = []
  try {
    const meta: Meta = JSON.parse(
      await getCmdOutput("cargo", ["metadata", "--all-features", "--format-version", "1", ...extraArgs], {
        cwd: this.root,
      }),
    )
    // ...
  } catch (err) {
    console.error(err)
  }
  return packages
}
```

### `/src/config.ts`
指定された `workspaces` オプション値をパースして `Workspace` インスタンスを生成する処理があります。
```ts:/src/config.ts
    // Constructs the workspace config and paths to restore:
    // The workspaces are given using a `$workspace -> $target` syntax.

    const workspaces: Array<Workspace> = [];
    const workspacesInput = core.getInput("workspaces") || ".";
    for (const workspace of workspacesInput.trim().split("\n")) {
      let [root, target = "target"] = workspace.split("->").map((s) => s.trim());
      root = path.resolve(root);
      target = path.join(root, target);
      workspaces.push(new Workspace(root, target));
    }
    self.workspaces = workspaces;
```

これら2つの処理を見ると、`this.root` には、 `workspaces` オプションで指定された **`xxxxx -> target` のうち `xxxxx` の部分が `root` として設定される**ようです。

逆に言うと、ユーザーが `workspaces` を指定していないと `this.root` は `.` のトップレベル（GitHub Actions が回っているリポジトリ直下）と判断されるようになります。

また、`this.root` を決定する処理において、 `working-directory` は参照されていないこともわかりました。

私としては「`working-directory` を設定すれば OK なのでは？」という思い込みがあったので、この挙動はちょっと意外でした。

# エラーの原因と解決策

rust-cache の `workspaces` オプションを指定しないと、`cwd` がトップレベルとみなされて `cargo metadata` が動き続けるので、サブディレクトリの `Cargo.toml` を見つけられずにエラーが出ていた　というのが原因でした。

解決策としては、以下のように `workspaces` オプションで Rust のプロジェクトディレクトリを指定すれば OK です。

```yaml: 🟢 OK（rust-ci.yml）
defaults:
  run:
    shell: bash
    working-directory: backend

jobs:
  check:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Cache dependencies
        uses: Swatinem/rust-cache@v2
        # 追加
        with:
          workspaces: |
            backend -> target
```

# `Swatinem/rust-cache` のキャッシュが効くようになった

## 1 回目の CI 実行

🟢OK の YAML を push し、Github Actions を実行したところ、該当のエラーは解消されている（ちゃんと backend 配下の `Cargo.toml` を見つけられている）ことを確認しました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/32a9ec28-53a1-1c57-f091-3c2d7b0e4e32.png)

後続の `Post Cache dependencies` でキャッシュが保存されています。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/43700b47-669e-17be-e34f-b1cdcb34d0dd.png)

## 2 回目の CI 実行

続けて、ソースコードを修正して push し、Github Actions の実行結果（`Cache dependencies` ステップ）のログをみると、キャッシュが効いていることがわかります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/df5da200-1475-201e-62ef-77eb5708a4a2.png)

2 回目はキャッシュが効いているので速いです。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/319375/5cdb1457-d774-81f5-e9ec-a872e9383302.png)

# さいごに

モノレポ環境のサブディレクトリで Rust プロジェクトを管理していて、リポジトリ直下に `Cargo.toml` を置いていない場合は `workspaces` オプションの設定をお忘れなく！


ちなみに、一通り調査を終えた後に知ったのですが、明確な回答が以下にありました。

https://github.com/Swatinem/rust-cache/issues/133

`rust-cache@v1` だと `working-directory` が機能していたらしいですが、より柔軟な設定ができるよう、`rust-cache@v2` から `workspaces` オプションに置き換えられたようです。
