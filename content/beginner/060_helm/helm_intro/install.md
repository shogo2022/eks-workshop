---
title: "Helm CLIのインストール"
date: 2018-08-07T08:30:11-07:00
weight: 5
---
<!--
## Install the Helm CLI
-->
## Helm CLIのインストール

<!--
Before we can get started configuring Helm, we'll need to first install the
command line tools that you will interact with. To do this, run the following:
-->
Helmの設定を開始する前に、これから使用するコマンドラインツールをインストールする必要があります。
次を実行してください:

```sh
curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

<!--
We can verify the version
-->
バージョンを確認します

```sh
helm version --short
```

<!--
Let's configure our first Chart repository. Chart repositories are similar to
APT or yum repositories that you might be familiar with on Linux, or Taps for
Homebrew on macOS.
-->

<!--
Download the `stable` repository so we have something to start with:
-->
`stable` レポジトリをダウンロードして始めましょう:

```sh
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

<!--
Once this is installed, we will be able to list the charts you can install:
-->
これがインストールされたら、インストールできるchartを表示できます:

```sh
helm search repo stable
```

<!--
Finally, let's configure Bash completion for the `helm` command:
-->
最後に、 `helm` コマンドのBash completionを設定しましょう:

```sh
helm completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
source <(helm completion bash)
```
