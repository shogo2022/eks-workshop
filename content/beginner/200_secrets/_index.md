---
title: "SealedSecretsによるsecretの保護"
chapter: true
weight: 200
tags:
  - beginner
  - CON206
---

<!--
# Securing Secrets using SealedSecrets
-->
# SealedSecretsによるsecretの保護

<!--
[Kubernetes Secret](https://kubernetes.io/docs/concepts/configuration/secret/) is a resource that helps cluster operators manage the deployment of sensitive information such as passwords, OAuth tokens, and ssh keys etc. These Secrets can be mounted as data volumes or exposed as environment variables to the containers in a Pod, thus decoupling Pod deployment from managing sensitive data needed by the containerized applications within a Pod. 
-->
[Kubernetes Secret](https://kubernetes.io/docs/concepts/configuration/secret/)はクラスタ運用者がパスワード、OAuthトークンやSSH鍵などの機密情報を扱う際に使用するリソースです。Secretはpodの中のコンテナに対して、データボリュームとしてマウントするか環境変数として組み込むことができるので、podのデプロイメントと機密情報の取り扱いを切り離すことができます。

<!--
It is a common practice for a DevOps Team to manage the YAML manifests for various Kubernetes resources and version control them using a Git repository. Additionally, they can integrate a Git repository with a [GitOps workflow to do Continuous Delivery](https://eksworkshop.com/intermediate/260_weave_flux/) of such resources to an EKS cluster. The challenge here is about managing the YAML manifests for Kubernetes Secrets outside the cluster. The sesitive data in a Secret is obfuscated by using merely base64 encoding. Storing such files in a Git repository is extremely insecure as it is trivial to decode the base64 encoded data. 
-->
DevOpsチームにとって、KubernetesリソースのYAMLマニフェストファイルをGitレポジトリでバージョン管理するのは一般的なことです。加えて、[GitOps workflowによる継続的デリバリー](https://eksworkshop.com/intermediate/260_weave_flux/)をGitレポジトリと連携させ、リソースをEKSクラスタに反映させることもできます。ここで問題となるのは、クラスタ外でのSecretsのYAMLマニフェストファイルの管理についてです。Secretの中の機密情報はbase64で難読されているだけです。base64の復号は非常に簡単なので、このようなファイルをGitレポジトリに置くのはとても危険です。

<!--
[Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) provides a mechanism to encrypt a Secret object so that it is safe to store - even to a public repository. A SealedSecret can be decrypted only by the controller running in the Kubernetes cluster and nobody else is able to obtain the original Secret from a SealedSecret. In this Chapter, you will use SealedSecrets to encrypt YAML manifests pertaining to Kubernetes Secrets as well as be able deploy these encrypted Secrets to your EKS clusters using normal workflows with tools such as [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).
-->
[Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets)はSecretオブジェクトをパブリックレポジトリに置いても安全なように、暗号化する機能を提供します。SealedSecretはKubernetesクラスタ上のコントローラからのみ復号でき、他からSealedSecretから元のSecretを取り出すことはできません。この章では、SealedSecretsを使ってSecretが入ったYAMLマニフェストを暗号化し、その暗号化されたSecretを[kubectl](https://kubernetes.io/docs/reference/kubectl/overview/)などのツールを使った一般的なフローでEKSクラスタにデプロイします。