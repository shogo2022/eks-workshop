[![Gitpod Ready-to-Code](https://img.shields.io/badge/Gitpod-Ready--to--Code-blue?logo=gitpod)](https://gitpod.io/#https://github.com/aws-samples/eks-workshop) 

# eksworkshop

### Setup:
#### Using GitPod.io:

<!--
This is how I set up my environment:
(I am using gitpod.io for editing)
-->
環境の構築方法:
(gitpod.ioを使った編集)

<!--
1. fork the repo to your own github account
2. prepend `gitpod.io#` to the beginning of your github url. Mine becomes: `https://gitpod.io#github.com/brentley/eks-workshop`
3. once gitpod has started, in the terminal, run `npm install && npm run theme`
This will install the dependencies and clone the theme submodule.
-->
1. 自分のgithubアカウントにリポジトリをフォークします。
2. 自身のgithub urlの前に `gitpod.io#` をつけます。私の場合は、 `https://gitpod.io#github.com/brentley/eks-workshop` になります。
3. gitpodが起動したら、ターミナルで `npm install && npm run theme` と入力します。
これは依存パッケージのインストールとテーマサブモジュールのクローンを行います。

<!--
From here, you can use the online IDE to edit /content/chapter/filename.md...
If you want to preview your edits, in the terminal, run:
`npx hugo server`.
That will start the local hugo server.
-->
オンラインIDEを使って/content/chapter/filename.mdを編集できるようになりました。
編集後にプレビューをみるときは、ターミナルで以下を実行します:
`npx hugo server`
ローカルのhugoサーバが起動します。

<!--
A dialog box will pop up telling you "A service is listening on port 1313, but is not
exposed." -- press the expose button. After that, choose "open browser" to get a new
tab with your preview site. As you save edits, this tab should refresh.
-->
ポップアップしたダイアログボックスで"A service is listening on port 1313, but is not
exposed."と出るので、exposeボタンを押します。その後に"open browser"を選ぶと新しいタブでプレビューが表示されます。
変更を保存すると、このタブも更新されるはずです。

<!--
When you're happy with your edits, commit, push, and open a pull request to the upstream
repo's main branch. Once merged, the preview site (linked above) will be refreshed.
-->

<!--
#### On a Mac:
Install Hugo:
`brew install hugo`
-->
#### On a Mac:
Hugoのインストール:
`brew install hugo`

<!--
#### On Linux:
  - Download from the releases page: https://github.com/gohugoio/hugo/releases/tag/v0.64.1
  - Extract and save the executable to `/usr/local/bin`
-->
#### On Linux:
  - リリースページからダウンロードします: https://github.com/gohugoio/hugo/releases/tag/v0.64.1
  - 展開後、実行ファイルを`/usr/local/bin`に置きます。

#### Clone this repo:
From wherever you checkout repos:
`git clone git@github.com:aws-samples/eks-workshop.git` (or your fork)


#### Clone the theme submodule:
`cd eks-workshop`

`git submodule init` ;
`git submodule update`

#### Install Node.js and npm:
You can follow instructions from npm website: https://www.npmjs.com/get-npm

#### Install node packages:
`npm install`

#### Run Hugo locally:
`npm run server`
or
`npm run drafts` to see stubbed in draft pages.

`npm run build` will build your content locally and output to `./public/`

`npm run test` will test the built content for bad links

#### View Hugo locally:
Visit http://localhost:8080/ to see the site.

#### Making Edits:
As you save edits to a page, the site will live-reload to show your changes.

#### Auto Deploy:
Any commits to main will auto build and deploy in a couple of minutes. You can see the currently
deployed hash at the bottom of the menu panel.

Any commits to a branch will auto build and deploy in a couple of minutes to a custom route named with the branch name. You can see the currently
deployed hash at the bottom of the menu panel.
An example is the "jenkinsworld" branch would be deployed to https://eksworkshop.com/jenkinsworld/

note: shift-reload may be necessary in your browser to reflect the latest changes.