---
title: "マニフェストでのデプロイ"
date: 2018-10-087T08:30:11-07:00
weight: 25
draft: false
---

<!--
Now we are ready to use Weave Flux to deploy the hello world application into our Amazon EKS cluster.  To do this we will clone our GitHub config repository (k8s-config) and then commit Kubernetes manifests to deploy. 
-->
これで、Weave FLuxを使ってAmazon EKS クラスタにhello worldアプリケーションをデプロイする準備が整いました。GitHubのコンフィグレポジトリ(k8s-config)をクローンして、マニフェストをコミットしてデプロイします。

```
cd ..
git clone https://github.com/${YOURUSER}/k8s-config.git     
cd k8s-config
mkdir charts namespaces releases workloads
```

<!--
Create a namespace Kubernetes manifest. 
-->
名前空間用のマニフェストファイルを作成します。

```
cat << EOF > namespaces/eks-example.yaml
apiVersion: v1
kind: Namespace
metadata:
  labels:
    name: eks-example
  name: eks-example
EOF
```

<!--
Create a deployment Kubernetes manifest.  
-->
deployment用のマニフェストファイルを作成します。

<!--
{{% notice info %}}
Update the image below to point to your ECR repository and image tag (**Do NOT use latest**).  You can find your Image URI from the [Amazon ECR Console](https://console.aws.amazon.com/ecr/repositories/eks-example/).  Replace YOURACCOUNT and YOURTAG)
{{% /notice %}}
-->
{{% notice info %}}
下のimageを自身のECRレポジトリに変更し、タグ( **latestは使わない** )も変更してください。自身のイメージURIは[Amazon ECRコンソール](https://console.aws.amazon.com/ecr/repositories/eks-example/)から確認できます。YOURACCOUNTとYOURTAGを変更すること。
{{% /notice %}}

```
cat << EOF > workloads/eks-example-dep.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eks-example
  namespace: eks-example
  labels:
    app: eks-example
  annotations:
    # Container Image Automated Updates
    flux.weave.works/automated: "true"
    # do not apply this manifest on the cluster
    #flux.weave.works/ignore: "true"
spec:
  replicas: 1
  selector:
    matchLabels:
      app: eks-example
  template:
    metadata:
      labels:
        app: eks-example
    spec:
      containers:
      - name: eks-example
        image: YOURACCOUNT.dkr.ecr.us-east-1.amazonaws.com/eks-example:YOURTAG
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
          name: http
          protocol: TCP
        livenessProbe:
          httpGet:
            path: /
            port: http
        readinessProbe:
          httpGet:
            path: /
            port: http
EOF
```

<!--
Above you see 2 Kubernetes annotations for Flux.  
-->
ここではFluxに関わるannotationが2つあるのが確認できます。

<!--
* flux.weave.works/automated tells Flux whether the container image should be automatically updated.  
* flux.weave.works/ignore is commented out, but could be used to tell Flux to temporarily ignore the deployment.  
-->
* flux.weave.works/automated はコンテナイメージが自動的にアップデートされるかをFluxに指示します。
* flux.weave.works/ignore はここではコメントアウトされていますが、デプロイメントを一時的に無視するようにFluxに指示する時に使えます。

<!--
Finally, create a service manifest to enable a load balancer to be created.
-->
さあ以後にロードバランサーを作成するためのserviceマニフェストを作成します。

```
cat << EOF > workloads/eks-example-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: eks-example
  namespace: eks-example
  labels:
    app: eks-example
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app: eks-example
EOF
```

<!--
Now commit the changes and push to your repository.  
-->
変更をコミットしてレポジトリにpushします。

```
git add . 
git commit -am "eks-example-deployment"
git push 
```

<!--
Check the logs of your Flux pod.  It will pull config from the k8s-config repository every 5 minutes.  Ensure you replace the pod name below with the name in your deployment.  
-->
Flux podのログを確認します。5分ごとにk8s-configレポジトリからコンフィグをpullしています。pod名は自身のdeploymentに合わせてpod名を変更してください。

```
kubectl get pods -n flux

kubectl logs flux-5bd7fb6bb6-4sc78 -n flux
```

<!--
Now get the URL for the load balancer (LoadBalancer Ingress) and connect via your browser (this may take a couple minutes for DNS).
-->
ロードバランサー(LoadBalancer Ingress)のURLを確認して、ブラウザから接続してみましょう(DNSでアクセスできるようになるまで数分かかるかもしれません)。

```
kubectl describe service eks-example -n eks-example
```

<!--
Make a change to the eks-example source code and push a new change.  
-->
eks-exampleのソースコードを変更して、pushしてみます。

<!--
```
cd ../eks-example
vi src/index.html
   # Change the <title> AND <h> to Hello World Version 2

git commit -am "v2 Updating home page"
git push
```
-->
```
cd ../eks-example
vi src/index.html
   # <title>と<h>を"Hello World Version 2"に変更します

git commit -am "v2 Updating home page"
git push
```

<!--
Now you can watch in the [CodePipeline console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines) for the new image build to complete.  This will take a couple minutes.  Once complete, you will see a new image land in your [Amazon ECR repository](https://console.aws.amazon.com/ecr/repositories/eks-example/). 
Monitor the **kubectl logs** for the Flux pod and you should see it update the configuration within five minutes.  
-->
新しいイメージのビルドは[CodePipeline console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)で確認できます。これには数分かかりますが、完了すると[Amazon ECR repository](https://console.aws.amazon.com/ecr/repositories/eks-example/)に新しいイメージが出てきます。
Flux podの **kubectl logs** を見ると、5分以内にコンフィグがアップデートされるのが見えるはずです。

<!--
Verify the web page has updated by refreshing the page in your browser.  
-->
ブラウザ上でページを更新して、アップデートされていることを確認します。

<!--
Your boss calls you late at night and tells you that people are complaining about the deployment.  We need to back it out immediately!  We could modify the code in eks-example and trigger a new image build and deploy.  However, we can also use git to revert the config change in k8s-config.  Lets take that approach.
-->
上司が深夜に電話をかけてきました。デプロイメントに問題があるようです。すぐにでもロールバックしなければなりません!eks-exampleのコードを編集し、新しいイメージのビルドとデプロイをすることもできますが、gitを使ってk8s-configへの変更を戻すことができます。やってみましょう。

```
cd ../k8s-config
git pull 

git log --oneline

git revert HEAD
   # Save the commit message

git log --oneline 

git push
```

<!--
You should now be able to watch logs for the Flux pod and it will pull the config change and roll out the previous image.  Check your URL in the browser to ensure it is reverted.  
-->
Flux podのログを見ると、コンフィグをpullして以前のイメージがロールアウトされるのが見えるはずです。ブラウザで戻っていることを確認します。

<!--
Phew!  Disaster averted.  
-->
ふぅ! 大障害は免れたようです。

