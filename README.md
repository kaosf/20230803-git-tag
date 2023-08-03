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
