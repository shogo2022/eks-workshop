---
title: "アプリケーションのアップデート"
weight: 40
draft: false
---

<!--
Our application is now deployed into our ArgoCD. We are now going to update our github repository synced with our application
-->
アプリケーションがArgoCDでデプロイされました。では、アプリケーションと同期されているgithubレポジトリをアップデートしてみましょう

<!--
### Update your application
-->
### アプリケーションのアップデート
<div data-proofer-ignore>
<!--
Go to your Github fork repository (replace GITHUB_USERNAME with your own):
-->
自身のGithubフォークを開きます(GITHUB_USERNAMEを獅子員のものに変更してください):
https://github.com/GITHUB_USERNAME/ecsdemo-nodejs/blob/master/kubernetes/deployment.yaml
</div>

<!--
Update `spec.replicas: 2`
-->
`spec.replcas: 2` にアップデートします
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ecsdemo-nodejs
  labels:
    app: ecsdemo-nodejs
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: ecsdemo-nodejs
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: ecsdemo-nodejs
    spec:
      containers:
      - image: brentley/ecsdemo-nodejs:latest
        imagePullPolicy: Always
        name: ecsdemo-nodejs
        ports:
        - containerPort: 3000
          protocol: TCP
```
<!--
Add a commit message and click on `Commit changes`
-->
コミットメッセージを入力し、`Commit changes` をクリックします

<!--
### Access ArgoCD Web Interface
To deploy our change we can access to ArgoCD UI. Open your web browser and go to the Load Balancer url:
-->
### ArgoCDウェブインタフェースへのアクセス
変更をデプロイするには、ArgoCD UIにアクセスします。ブラウザで、ロードバランサーのURLを開きます:
```
echo $ARGOCD_SERVER
```
<!--
Login using `admin` / `$ARGO_PWD`.
You now have access to the ecsdemo-nodejds application. After clicking to `refresh` button status should be `OutOfSync`:
-->
`admin` / `$ARGO_PWD` でログインします。
ecsdemo-nodejsアプリケーションが見えます。 `refresh` を押してステータスを確認すると`OutOfSync` になっているはずです:

![Fork url](/images/argocd/app_outofsync.png)

<!--
This means our Github repository is not synchronised with the deployed application. To fix this and deploy the new version (with 2 replicas) click on the `sync` button, and select the `APPS/DEPLOYMENT/DEFAULT/ECSDEMO-NODEJS` and `SYNCHRONIZE`:
-->
これはGithubレポジトリとデプロイされているアプリケーションが同期されていないことを示しています。これを直して(2レプリカの)新しいバージョンをデプロイするには、 `sync` をクリックし、`APPS/DEPLOYMENT/DEFAULT/ECSDEMO-NODEJS` と `SYNCHRONIZE` を選択します:

![Fork url](/images/argocd/app_sync.png)

<!--
After the sync completed our application should have the `Synced` status with 2 pods:
-->
同期が完了すると、アプリケーションは `Synced` となり、podが2つになっているはずです:

![Fork url](/images/argocd/app_synced.png)

<!--
All those actions could have been made with the Argo CLI also. 
-->
これらの作業は全てArgo CLIでも行うことができます。
