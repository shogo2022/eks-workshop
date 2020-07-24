---
title: "Secretの作成とデプロイ"
date: 2019-04-09T00:00:00-03:00
weight: 10
draft: false
---

<!--
Since 1.14, Kubectl supports the management of Kubernetes objects using Kustomize. [Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#overview-of-kustomize) provides resource Generators to create Secrets and ConfigMaps. The Kustomize generators should be specified in a **kustomization.yaml** file. A Kustomize file for generating a Secret from literal key-value pairs looks as follows:
-->
kubectlは1.14からKubernetesオブジェクトの管理に、SecretとConfigMapを生成するためのリソースジェネレータ機能を持つ[Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#overview-of-kustomize)をサポートしています。 **kustomization.yaml** の中にKustomizeで使うジェネレータが特定されています。文字列のキー・バリューペアからSecretを生成するためのKustomizeファイルは次の通りです。

{{< output >}}
namespace: octank
secretGenerator:
- name: database-credentials
  literals:
  - username=admin
  - password=Tru5tN0!
generatorOptions:
  disableNameSuffixHash: true
{{< /output >}}

<!--
Run the following set of commands to generate a Secret using Kubectl and Kustomize.
-->
次のコマンドでKubectlとKustomizeを使い、Secretを生成します。
```
mkdir -p ~/environment/secrets
cd ~/environment/secrets
wget https://eksworkshop.com/beginner/200_secrets/secrets.files/kustomization.yaml
kubectl kustomize . > secret.yaml
```

<!--
The generated Secret with base64 encoded value for *username* and *password* keys is as follows:
-->
*username* と *password* の値がBase64でエンコードされ、生成されたSecretは次のようになっています:
{{< output >}}
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: database-credentials
  namespace: octank
data:
  password: VHJ1NXROMCE=
  username: YWRtaW4=
{{< /output >}}

<!--
You can now deploy this Secret to your EKS cluster.
-->
このSecretをEKSクラスタにデプロイします。
```
kubectl create namespace octank
kubectl apply -f secret.yaml
```

<!--
#### Exposing Secrets as Environment Variables
You may expose the keys, namely, *username* and *password*, in the **database-credentials** Secret to a Pod as environment variables using a Pod manifest as shown below:
-->
#### 環境変数としてSecretを使う
次のようにPodマニフェストに書くことでSecretのキー、ここでは  **database-credentials** 内の *username* と *password* をPodに環境変数として与えることができます:
{{< output >}}
apiVersion: v1
kind: Pod
metadata:
  name: someName
  namespace: someNamespace
spec:
  containers:
  - name: someContainer
    image: someImage
    env:
    - name: DATABASE_USER
      valueFrom:
        secretKeyRef:
          name: database-credentials
          key: username
    - name: DATABASE_PASSWORD
      valueFrom:
        secretKeyRef:
          name: database-credentials
          key: password 
{{< /output >}}

<!--
Run the following set of commands to deploy a pod that references the **database-credentials** Secret created above.
-->
次のコマンドを実行して、作成されたばかりの **database-credentials** を参照するpodをデプロイします。
```
wget https://eksworkshop.com/beginner/200_secrets/secrets.files/pod-variable.yaml
kubectl apply -f pod-variable.yaml
kubectl get pod -n octank
```

<!--
View the output logs from the pod to verfiy that the environment variables *DATABASE_USER* and *DATABASE_PASSWORD* have been assigned the expected literal values
-->
podから出力されたログを確認し、 *DATABASE_USER* と *DATABASE_PASSWORD* の環境変数が期待した通りの文字列になっているか確認します
```
kubectl logs pod-variable -n octank
```
<!--
The output should look as follows:
-->
出力はこのようになっているはずです:
{{< output >}}
DATABASE_USER = admin
DATABASE_PASSWROD = Tru5tN0!
{{< /output >}}
