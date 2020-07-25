---
title: "Argo CDのインストール"
weight: 10
draft: false
---

<!--
### ArgoCD Architecture
-->
### ArgoCDの構造
![ArgoCD Architecture](/images/argocd/argocd_architecture.png)

<!--
ArgoCD is composed of three mains components:
-->
ArgoCDは主に3つの部品から構成されています:

<!--
***API Server***: Exposes the API for the WebUI / CLI / CICD Systems
-->
***APIサーバ*** : WebUI / CLI / CICDシステムのための APIを公開

<!--
***Repository Server***: Internal service which maintains a local cache of the git repository holding the application manifests
-->
***レポジトリサーバ*** : アプリケーションマニフェストを保存するgitレポジトリのローカルキャッシュを管理する内部サービス

<!--
***Application Controller***: Kubernetes controller which controls and monitors applications continuously and compares that  current live state with desired target state (specified in the repository). If a `OutOfSync` is detected, it will take corrective actions.
-->
***アプリケーションコントローラ*** : アプリケーションを継続的位に監視し、(レポジトリに定義された)望ましい状態と現状を比較するKubernetesコントローラで、 `OutOfSync` が検知されると是正措置を実行する

<!--
### Install Argo CD
-->
### Argo CDのインストール

<!--
All those components could be installed using a manifest provided by the Argo Project:
-->
全ての構成物はArgoプロジェクトが提供しているマニフェストファイルでインストールできます:

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<!--
### Install Argo CD CLI
-->
### Argo CD CLIのインストール

<!--
To interact with the `API Server` we need to deploy the CLI:
-->
`APIサーバ` とやりとりをするためには、CLIが必要です:

```
VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')

sudo curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64

sudo chmod +x /usr/local/bin/argocd
```
