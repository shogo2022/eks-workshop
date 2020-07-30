---
title: "Helmでのインストール"
date: 2018-10-087T08:30:11-07:00
weight: 30
draft: false
---

<!--
You can use this same approach to deploy Helm charts.  These charts can exist within the configuration Git repository (k8s-config), or hosted from an external chart repository.  In this example we will use an external chart to keep things simple.  
-->
同じことがHelm chartsでもできます。このchartsはコンフィグが保存されているGitレポジトリ(k8s-config)にも、外部のchartレポジトリにもいれることができます。この例では、シンプルに外部のchartを使います。

<!--
In your k8s-config directory, create a namespace manifest.
-->
k8s-configディレクトリで、名前空間マニフェストを作成します。

<!--
{{% notice info %}}
The **git pull** command ensures we have the latest configuration in case Flux modified anything.
{{% /notice %}}
-->
{{% notice info %}}
**git pull** コマンドで最新のコンフィグになっていることを確認します。
{{% /notice %}}

```
cd ../k8s-config

git pull 

cat << EOF > namespaces/nginx.yaml
apiVersion: v1
kind: Namespace
metadata:
  labels:
    name: nginx
  name: nginx
EOF
```

<!--
Now create a Helm release manifest.  This is a custom resource definition provided by Weave Flux. 
-->
Helmのリリースマニフェストを作成しましょう。これはWeave Fluxが提供するcustom resource definitionです。

```
cat << EOF > releases/nginx.yaml
---
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: mywebserver
  namespace: nginx
  annotations:
    flux.weave.works/automated: "true"
    flux.weave.works/tag.nginx: semver:~1.16
    flux.weave.works/locked: 'true'
    flux.weave.works/locked_msg: '"Halt updates for now"'
    flux.weave.works/locked_user: User Name <user@example.com>
spec:
  releaseName: mywebserver
  chart:
    repository: https://charts.bitnami.com/bitnami/
    name: nginx
    version: 3.3.2
  values:
    usePassword: true
    image:
      registry: docker.io
      repository: bitnami/nginx
      tag: 1.16.0-debian-9-r46
    service:
      type: LoadBalancer
      port: 80
      nodePorts:
        http: ""
      externalTrafficPolicy: Cluster
    ingress:
      enabled: false
    livenessProbe:
      httpGet:
        path: /
        port: http
      initialDelaySeconds: 30
      timeoutSeconds: 5
      failureThreshold: 6
    readinessProbe:
      httpGet:
        path: /
        port: http
      initialDelaySeconds: 5
      timeoutSeconds: 3
      periodSeconds: 5
    metrics:
      enabled: false
EOF
```

<!--
You will notice a few additional annotations above.  
-->
いくつかのannotationがあるのに気づいたでしょうか。

<!--
* flux.weave.works/locked tells Flux to lock the deployment so a new image version will not be deployed.  
* flux.weave.works/tag.nginx filters the images available by semantic versioning.   
-->
* flux.weave.works/locked　は新しいイメージのデプロイを止め、FLuxにDeploymentをロックするように伝えます 
* flux.weave.works/tag.nginx はセマンティックバージョニングの中で使用可能なイメージをフィルタします

<!--
Now commit the changes and wait up to 5 minutes for Flux to pull in the configuration.  
-->
変更をコミットして、Fluxがコンフィグをpullするまで5分待ちます。

```
git add . 
git commit -am "Adding nginx helm release"
git push
```

<!--
Verify the deployment as follows. 
-->
deploymentを以下のように確認します。

<!--
{{% notice info %}}
Use your pod name below for **kubectl logs**
{{% /notice %}}
-->
{{% notice info %}}
以下の **kubectl logs** では自身のpod名を使ってください
{{% /notice %}}

```
kubectl get pods -n flux
kubectl logs flux-5bd7fb6bb6-4sc78 -n flux

helm list
kubectl get all -n nginx
```

<!--
If this doesn't deploy, check to ensure helm was initialized.  Also, look at the Flux Helm operator to see if there are any errors.  
-->
これがデプロイされない場合は、helmが初期化されていることを確認してください。Flux Helm operatorでエラーがないかも確認してください。

```
kubectl get pods -n flux
kubectl logs flux-helm-operator-df5746688-84kw8 -n flux
```

<!--
You've now seen how Weave Flux can enable a GitOps approach to deployment. 
-->
これでWeave Fluxが、どのようにGitOpsに使えるかがわかりました。