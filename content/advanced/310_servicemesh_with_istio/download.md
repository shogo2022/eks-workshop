---
title: "Istio CLIのダウンロードとインストール"
date: 2018-11-13T16:36:43+09:00
weight: 20
draft: false
---

<!--
Before we can get started configuring Istio we’ll need to first install the command line tools that you will interact with. To do this run the following.
-->
Istioを設定する前に、コマンドラインツールをインストールする必要があります。次を実行します。

{{% notice info %}}
<!--
We will use istio version `1.5.2`
-->
ここではistioバージョン `1.5.2` を使います
{{% /notice %}}

```bash
echo 'export ISTIO_VERSION="1.5.2"' >> ${HOME}/.bash_profile
source ${HOME}/.bash_profile
```

```bash
cd ~/environment
curl -L https://istio.io/downloadIstio | sh -
```

<!--
The installation directory contains:
-->
このディレクトリには次のファイルが含まれています:

<!--
* Installation YAML files for Kubernetes in install/kubernetes
* Sample applications in `samples/`
* The `istioctl` client binary in the `bin/` directory (`istioctl` is used when manually injecting Envoy as a sidecar proxy).
-->
* install/kubernetes配下にKubernetes用のYAMLファイル
* `sanples/` 配下にサンプルアプリケーション
* `bin/` ディレクトリに `istioctl` のクライアントバイナリ( `istioctl` はEnvoyをサイドカープロキシとして手動で入れるときに使います)

```bash
cd ${HOME}/environment/istio-${ISTIO_VERSION}

sudo cp -v bin/istioctl /usr/local/bin/
```

<!--
We can verify that we have the proper version in our $PATH
-->
適切なバージョンが$PATHに含まれているかを確認します

```bash
istioctl version --remote=false
```
