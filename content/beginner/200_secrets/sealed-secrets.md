---
title: "KubernetesのSealed Secrets"
date: 2019-04-09T00:00:00-03:00
weight: 12
draft: false
---

<!--
#### How it Works
Sealed Secrets is composed of two parts:
- A cluster-side controller
- A client-side utility called *kubeseal*
-->
#### 仕組み
Sealed Secretsには2つの部品があります:
- クラスターサイドコントローラ
- クライアントサイドユーティリティ ( *kubeseal* )

<!--
Upon startup, the controller looks for a cluster-wide private/public key pair, and generates a new 4096 bit RSA key pair if not found. The private key is persisted in a Secret object in the same namespace as that of the controller. The public key portion of this is made publicly available to anyone wanting to use SealedSecrets with this cluster.
-->
コントローラは起動の際に、クラスタ全体の公開/秘密鍵ペアを探し、見つからなければ新しく4096bitのRSAキーペアを生成します。秘密鍵はコントローラと同じ名前空間にSecretオブジェクトとして保存されます。公開鍵はクラスタ内で誰にでも使えるように公開されています。

<!--
During encryption, each value in the original Secret is symmetrically encrypted using AES-256 with a randomly-generated session key. The session key is then asymmetrically encrypted with the controller's public key using SHA256 and the original Secret's namespace/name as the input parameter. The output of the encryption process is a string that is constructed as follows:  
*length (2 bytes) of  encrypted session key + encrypted session key + encrypted Secret*
-->
暗号化では、元々のSecret内の各値はランダムに生成されたセッションキーでAES-256で対称的に暗号化されます。セッションキーはコントローラの公開鍵と元のSecretの名前空間/名前を入力として非対称に暗号化されます。暗号プロセスの文字列は次のようになっています:
*暗号化されたセッションキーの長さ (2 bytes) + 暗号化されたセッションキー + 暗号化されたSecret*

<!--
When a SealedSecret custom resource is deployed to the Kubernetes cluster, the controller will pick it up, unseal it using the private key and create a Secret resource. During decryption, the SealedSecret's namespace/name is used again as the input parameter. This ensures that the SealedSecret and Secret are strictly tied to the same namespace and name.
-->
KubernetesにSealedSecretのカスタムリソースがデプロイされると、コントローラが秘密鍵を使ってSecretリソースを作成します。復号化の中で、SealedSecretの名前空間/名前が再び入力として使われます。これがSealedSecretとSecretが同じ名前空間と名前に紐づいくことを保証しています。

<!--
The companion CLI tool *kubeseal* is used for creating a SealedSecret custom resource definition (CRD) from a Secret resource definition using the public key. *kubeseal* can communicate with the controller through the Kubernetes API server and retrieve the public key needed for encrypting a Secret at run-time. The public key may also be downloaded from the controller and saved locally to be used offline.
-->
CLIツールの *kubeseal* はSecretのリソース定義から、公開鍵を使ってSeaaledSecretのカスタムリソース定義(custom resource definition - CRD)を作成するのに使われます。 *kubeseal* はKubernetes APIサーバ経由でコントローラと通信をし、ランタイムでSecretを暗号化するのに必要な公開鍵を取得します。コントローラから公開鍵をダウンロードし、オフラインで使うように保存することもできます。