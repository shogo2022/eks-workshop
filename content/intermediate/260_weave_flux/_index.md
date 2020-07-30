---
title: "Weave FluxでGitOps"
chapter: true
weight: 260
draft: false
tags:
  - advanced
  - operations
  - ci/cd
  - gitops
---

<!--
# GitOps with Weave Flux
-->
# Weave FluxでGitOps

<!--
[GitOps](https://www.weave.works/technologies/gitops/), a term coined by [Weaveworks](https://www.weave.works/), is a way to do [continuous delivery](https://aws.amazon.com/devops/continuous-delivery/).  Git is used as single source of truth for deploying into your cluster.  This is easy for a development team as they are already familiar with git and do not need to know other tools.  [Weave Flux](https://www.weave.works/oss/flux/) is a tool that runs in your Kubernetes cluster and implements changes based on monitoring Git and image repositories.
-->
[GitOps](https://www.weave.works/technologies/gitops/)は[Weaveworks](https://www.weave.works/)が使った造語で、[継続的デリバリー](https://aws.amazon.com/devops/continuous-delivery/)の方法を指します。クラスタへデプロイをする際の元としてGitが使われます。開発チームはgitに慣れており、他のツールを使う必要がないので、導入が簡単です。[Weave Flux](https://www.weave.works/oss/flux/)はKubernetesクラスタで稼働するツールで、Gitとイメージレポジトリを監視し、変更に応じて実装をします。

<!--
In this module, we will create a Docker image build pipeline using [AWS CodePipeline](https://aws.amazon.com/codepipeline/) for a sample application in a [GitHub](https://github.com/) repository. We will then commit Kubernetes manifests to GitHub and monitor Weave Flux managing the deployment.
-->
このモジュールでは、[GitHub](https://github.com/)レポジトリのサンプルアプリケーションを[AWS CodePipeline](https://aws.amazon.com/codepipeline/)を使って、Docker image build pipelineを作成します。その後に、マニフェストをGithubにコミットをして　Weave Fluxがdeploymentを操作する様子を確認します。

<!--
Below is a diagram of what will be created:
-->
作成していくもののイメージは下の通りです:

![GitOps Workflow](/images/weave_flux/gitops_workflow.png)
