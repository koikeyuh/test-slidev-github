---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev

# apply UnoCSS classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left

---

# Slidev公開手順

Github Pagesでの公開方法


<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
transition: fade-out
---

# Github Pagesでの公開

Github上で公開する方法になります

大まかな流れ

0. Githubアカウントがなければ作成（無料）
1. ローカルPCでSlidevプロジェクトを用意する
2. Github上で公開レポジトリを作る
3. ローカルのコードをgithubの作成したレポジトリへpushする
4. Github Pagesの設定
5. Github Actionsワークフローの作成
6. `https://<username>.github.io/<repository>` というURLで公開されます


ここまでできれば、ローカルPCでslidevで変更し、pushさえすれば、自動的に公開サイトにも変更がされます。

<!--
Here is another comment.
-->


---

# 0. Githubアカウントがなければ作成（無料）
アカウントをお持ちならスキップしてください

[Github](https://github.com/)にアクセスしてアカウントを作成してください



詳しくは、[GitHub でのアカウントの作成](https://docs.github.com/ja/get-started/start-your-journey/creating-an-account-on-github)


---

# 1. ローカルPCでSlidevプロジェクトを用意する
作成済みとしてスキップします

---

# 2. Github上で公開レポジトリを作る
レポジトリの名前を入力し、デフォルト設定はそのままでOK

Visibilityが`Public`になっていることを確認

![](/assets/github-create-new-repo.png)


---

# 3. コードをgithubの作成したレポジトリへpushする
ローカル端末で作成したslidevフォルダをgit管理します

0. `git`がなければインストール（Macならインストール済みだと思います。Windowsは[こちら](https://prog-8.com/docs/git-env-win)）
1. Mac：`ターミナル`、Windows：先程インストールした`Git Bash`を使用します。

```bash
cd /path/to/your-slidev-project

git config --global user.name "あなたの名前"
git config --global user.email "あなたのgithubに登録したメール"

git init
git branch -m main
echo "node_modules
dist
.DS_Store" >> .gitignore
git add .
git commit -m "initial commit"

git remote add origin https://github.com/<username>/<repo>.git
git push -u origin main
```

これで自分のgithub上で作成したレポジトリにローカルのファイルが反映されているはず。

---

# 4. Github Pagesの設定

Githubのサイトで作成したレポジトリでの作業になります


<img src="/assets/github-setting.png" width="700" />

---

# 4. Github Pagesの設定

Githubのサイトで作成したレポジトリでの作業になります

1. `Build and deployment`を`Github Actionis`へ変更
2. `Static HTML` > `Configure`をクリック

<img src="/assets/github-pages-setting.png" width="700" />


---

# 5. Github Actionsワークフローの作成
[Slidev公式サイトのHosting](https://sli.dev/guide/hosting)を参照

1. `Static HTML` > `Configure`をクリックしたときに作成される名前を`.github/workflows/deploy.yml`に変更
2. 中身を下記に書き換え、サイト右上の`Commit Changes...`（緑色ボタン）をクリック

<div class="scrollable-code">
```yaml
name: Deploy pages

on:
  workflow_dispatch:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 'lts/*'

      - name: Setup @antfu/ni
        run: npm i -g @antfu/ni

      - name: Install dependencies
        run: nci

      - name: Build
        run: nr build --base /${{github.event.repository.name}}/

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    needs: build
    runs-on: ubuntu-latest
    name: Deploy
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
</div>

---

# 5. Github Actionsワークフローの作成
続き

すると自動的にワークフローが走り、`dist`フォルダがビルドされて GitHub Pages に反映される。<br>
先程の、`Setting` > `Pages`のページにて、公開用URLが記載されている。

<img src="/assets/github-pages-url.png" width="650" />

---

# 編集したら

ローカルPCでスライドの修正をしたら、gitでadd, commit, pushすることで自動で変更が反映される

```bash
git add -A
git commit -m "update slides"
git push
```

---
layout: end
---

