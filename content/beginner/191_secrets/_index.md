---
title: "AWS Key Management Service (KMS) KeysでSecretを暗号化する"
chapter: true
weight: 191
pre: '<i class="fa fa-film" aria-hidden="true"></i> '
tags:
  - beginner
---

<!--
# Encrypting Kubernetes Secrets
-->
# Kubernetes Secretsの暗号化

{{< youtube CXbFkpugxyQ >}}

<!--
Kubernetes can store secrets that pods can access via a mounted volume. Today, Kubernetes secrets are stored with Base64 encoding, but security teams would prefer a stronger approach. Amazon EKS clusters version 1.13 and higher support the capability of encrypting your Kubernetes secrets using AWS Key Management Service (KMS) Customer Managed Keys (CMK). No changes in the way you are using secrets are required. The only requirement is to enable the encryption provider support during EKS cluster creation.
-->
Kubernetesではマウントされたボリュームを通して、podがsecretにアクセスできるようになっています。KubernetesではsecretはBase64で保存されていますが、セキュリティ要件によっては、強い暗号化が必要とされます。Amazon EKSクラスタ　バージョン1.13以上では、AWS Key Management Service (KMS) カスタマーマネージドキー(CMK)を使ってKubernetes secretを暗号化できるようになりました。secretを使う上での変更点はありません。使う上での要件は、EKSクラスタ作成の際にencryption providerのサポートを有効にするだけです。

![kms](/images/eks-secrets-flow-small-1-1024x621.png)

<!--
The workflow is as follows:
-->
ワークフローは次の通りです:

<!--
1. The user (typically in an admin role) creates a secret.
2. The Kubernetes API server in the EKS control plane generates a Data Encryption Key (DEK) locally and uses it to encrypt the plaintext payload in the secret. Note that the control plane generates a unique DEK for every single write, and the plaintext DEK is never saved to disk.
3. The Kubernetes API server calls ```kms:Encrypt``` to encrypt the DEK with the CMK. This key is the root of the key hierarchy, and, in the case of KMS, it creates the CMK on a hardware security module (HSM). In this step, the API server uses the CMK to encrypt the DEK and also caches the base64 of the encrypted DEK.
4. The API server stores the DEK-encrypted secret in etcd.
5. If one now wants to use the secret in, say a pod via a volume (read-path), the reverse process takes place. That is, the API server reads the encrypted secret from etcd and decrypts the secret with the DEK.
6. The application, running in a pod on either EC2 or Fargate, can then consume the secret as usual.
-->
1. ユーザ(通常はアドミン)がsecretを作成します。
2. EKSコントロールプレーンのKubernetes APIサーバはローカルにData Encryption Key (DEK)を生成し、secretの中のプレーンテキストを暗号化します。DEKは毎書き込みごとにコントロールプレーンによって生成され、プレーンテキストのDEKがディスクに浦オゾンされることはありません。
3. Kubernetes APIサーバはCMKでDEKを暗号化するために ```kms:Encrypt``` を呼びます。この鍵はキーヒエラルキーのルートで、KMSの場合には、ハードウェアセキュリティモジュール(HSM)上にCMKを作成します。このステップでは、APIサーバはCMKを使ってDEKを暗号化し、暗号化されたDEKのbase64をキャッシュします。
4. APIサーバはDEKで暗号化されたsecretをetcdに保存します。
5. secretを使う場合、例えばボリューム経由の場合は、逆の手順が踏まれます。APIサーバはetcdから暗号化されたsecretを読み込み、そのsecretをDEKで複合化します。
6. EC2やFargateで動いているpod上のアプリケーションは、いつもと同じようにsecretを使えます。
