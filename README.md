---
marp: true
class: invert
---
<!-- headingDivider: 1 -->

# Git tag

Gitのタグ機能およびそれを利用した成果物自動生成について

# Author

ka

![icon](https://www.kaosfield.net/icon.webp)

[kaosfield](https://www.kaosfield.net/)

![kaosfield QR](kaosfield-qr.svg)

# License

CC BY-NC-SA 4.0

[![CC BY-NC-SA 4.0](https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

Copyright (C) 2023 ka

# About this contents

GitHub Pages: [https://kaosf.github.io/20230803-git-tag](https://kaosf.github.io/20230803-git-tag)

Repository: [kaosf/20230803-git-tag - GitHub](https://github.com/kaosf/20230803-git-tag)

![GitHub Pages QR](gh-pages-qr.svg)

# タグの説明

SHA256によりプロジェクトのある時点でのスナップショットが一意に決まる

しかしとても人間が読めたものではない

ブランチはmainは常に動くしそれ以外は生成消滅がいつ起こるか分からない

そこで **タグ** を付ける機能が存在する

# タグ付け

```sh
git tag v1.0.0
```

# タグの削除

```sh
git tag -d v1.0.0
```

# タグのpush

```sh
git push origin v1.0.0
```

# pushしたタグの削除

```sh
git push origin :v1.0.0
# or
git push origin -d v1.0.0
```

# 実際に試してみよう

まずはリポジトリ用意

```sh
mkdir tag-experiment
cd tag-experiment

echo "Hello World" > README.md

git init
git add .
git commit -m "Initial commit"
```

※GUIツールを利用している人は適宜読み替えて下さい

# 実際に試してみよう 2

リポジトリ作成

`20230803-git-tag-practice` とする

```sh
git remote add origin git@github.com:kaosf/20230803-git-tag-practice.git
git push origin main -u
```

※以後ユーザ名 `kaosf` は各自のものに適宜修正して下さい

# 実際に試してみよう 3

更新する

```sh
echo "Version 1.0.0" >> README.md
git add .
git commit -m "Update"
git push
```

# 実際に試してみよう 4

タグをつけてpushしてみる

```sh
git tag v1.0.0
git push origin v1.0.0
```

https://github.com/kaosf/20230803-git-tag-practice/tags

見てみるとタグがリモートリポジトリ上でも出来ている

# 実際に試してみよう 5

タグを消して修正してみる

```sh
git tag -d v1.0.0
git push origin -d v1.0.0

echo "Fix" >> README.md
git add .
git commit -m "Fix"
git push

git tag v1.0.0
git push origin v1.0.0
```

# タグに応じて仕事をしてもらおう

タグがpushされたときに何かを生成してさらにそれを誰でもダウンロード可能な状態にしてみる

CIにはGitHub Actionsを用いることにする

# まずはモノを生成する土台を準備

`my-command` というコマンドを作る

実行するとタグで示すバージョンを表示してくれるツールにする

```sh
./my-command
#=> v1.0.0
```

# my-command 用意

```sh
cat <<'EOF' > my-command
#!/bin/bash
echo VERSION
EOF
chmod +x my-command
```

意味は後述

# my-command 用意 説明

`my-command` を作り内容を以下の通りにする

```sh
#!/bin/bash
echo VERSION
```

また実行可能ファイルとするため `chmod +x` を実行する

# ビルド内容

`VERSION` の部分を置き換えて

```sh
#!/bin/bash
echo v1.0.0
```

このようなファイルを生成することをビルド作業とする

# ビルドスクリプトを用意


```sh
cat <<'EOF' > build.sh
cat my-command | sed "s/VERSION/$1/" > dist/my-command
chmod +x dist/my-command
EOF
```

# ビルドスクリプトを用意 説明

`build.sh` を用意する

これを

```sh
bash build.sh vX.X.X
```

のように実行すれば `dist` ディレクトリに目的のモノが出来上がる想定である

※実際にはこれが `cargo build` だったり `make` になったりするであろう

# dist ディレクトリを用意しておく

```sh
mkdir dist
touch dist/.keep
cat <<'EOF' > dist/.gitignore
/*
!/.keep
EOF
```

# GitHub Actionsのための設定

`.github/workflows/build.yml`

```yml
name: build my-command
on:
  push:
    tags: [ 'v*.*.*' ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Build
      run: bash build.sh ${{ github.ref_name }}
    - name: Tar files
      run: tar -czvf dist/my-command.tar.gz -C dist my-command
    - uses: actions/upload-artifact@v3
      with:
        name: my-command
        path: dist/my-command.tar.gz
```

作るコマンドは後述

# GitHub Actionsのための設定 作成

```sh
mkdir -p .github/workflows
cat <<'EOF' > .github/workflows/build.yml
name: build my-command
on:
  push:
    tags: [ 'v*.*.*' ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Build
      run: bash build.sh ${{ github.ref_name }}
    - name: Tar files
      run: tar -czvf dist/my-command.tar.gz -C dist my-command
    - uses: actions/upload-artifact@v3
      with:
        name: my-command
        path: dist/my-command.tar.gz
EOF
```

# ここまでコミット

```sh
git add .
git commit -m "Impl my-command"
git push
```

# v1.0.1 を作ろう

```sh
git tag v1.0.1
git push origin v1.0.1
```

これでGitHub Actionsが動き v1.0.1 の `my-command.tar.gz` が出来上がる

GitHub Actionsのページからダウンロード出来る

発展内容としてリリースページを作ってそこに登録も自動で…等が出来ると良い

# おしまい

簡単な例を実際に自分の手で実行してみることはとても重要
