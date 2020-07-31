---
title: "Argo CLIのインストール"
date: 2018-11-18T00:00:00-05:00
weight: 30
draft: false
---

<!--
### Install Argo CLI
-->
### Argo CLIのインストール

<!--
Before we can get started configuring `argo` we'll need to first install the
command line tools that you will interact with. To do this run the following.
-->
`argo` を使う前に、コマンドラインツールをインストールする必要があります。次を実行します。

<!--
```bash
# set argo version
export ARGO_VERSION="v2.9.1"
```
-->
```bash
# argo バージョンの設定
export ARGO_VERSION="v2.9.1"
```

<!--
```bash
# Download the binary
sudo curl -sSL -o /usr/local/bin/argo https://github.com/argoproj/argo/releases/download/${ARGO_VERSION}/argo-linux-amd64

# Make binary executable
sudo chmod +x /usr/local/bin/argo

# Test installation
argo version --short
```
-->
```bash
# バイナリのダウンロード
sudo curl -sSL -o /usr/local/bin/argo https://github.com/argoproj/argo/releases/download/${ARGO_VERSION}/argo-linux-amd64

# バイナリを実行可能にする
sudo chmod +x /usr/local/bin/argo

# インストール後の確認
argo version --short
```

output
{{< output >}}
argo: v2.9.1
{{< /output >}}
