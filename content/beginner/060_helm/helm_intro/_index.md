---
title: "イントロダクション"
chapter: true
weight: 60
---

<!--
## Introduction
-->
## イントロダクション

<!--
[Helm](https://helm.sh/) is a package manager and application management tool
for Kubernetes that packages multiple Kubernetes resources into a single logical
deployment unit called a **Chart**.
-->
[Helm](https://helm.sh/)はKubernetes用のパッケージマネージャおよびアプリケーションマネージャで、
複数のKubernetesリソースを**Chart**と呼ばれる単一の設定単位にパッケージします。

<!--
Helm helps you to:
-->
Helmはこのようなときに使えます:

<!--
- Achieve a simple (one command) and repeatable deployment
- Manage application dependency, using specific versions of other application and services
- Manage multiple deployment configurations: test, staging, production and others
- Execute post/pre deployment jobs during application deployment
- Update/rollback and test application deployments
-->
- シンプル(コマンド一つ)かつ繰り返しのdeploymentを実行
- 複数のアプリケーションとサービスの特定バージョンに対するアプリケーション依存の管理
- 複数のdeployment設定の管理: テスト、ステージング、商用その他
- アプリケーション構築時の事前事後ジョブの実行
- テストアプリケーションのアップデート、ロールバック