---
title: "アプリケーションのデプロイ"
weight: 30
draft: false
---

<!--
We now have an ArgoCD fully deployed, we will now deploy an application (ecsdemo-nodejs).
-->
ArgoCDが完全にインストールできたので、アプリケーション(ecsdemo-nodeejs)をデプロイします。

<!--
### Fork application repository
First step is to create a fork for the Github application we will deploy.
-->
### アプリケーションレポジトリのフォーク
最初の手順は、デプロイするGitHubアプリケーションのフォーク作成です。

<!--
Login to github, go to: https://github.com/brentley/ecsdemo-nodejs.git and Fork the repo
-->
githubにログインし、https://github.com/brentley/ecsdemo-nodejs.git へ行き、レポジトリをフォークします
![Fork Img](/images/argocd/fork_app_repo.jpg)

<!--
Then into your select the https URL by clicking into button `Clone or download`:
-->
`Clone or download` ボタンを押して、https URLをコピーします:
![Fork url](/images/argocd/fork_url.jpg)

<!--
This URL will be needed when we will configure the application into ArgoCD.
-->
このURLはあとでArgoCDにアプリケーションを設定するときに必要になります。

<!--
### Create application
-->
### アプリケーション作成

<!--
Connect with ArgoCD CLI using our cluster context:
-->
クラスタコンテキストでArgoCD CLIを使う:
```
CONTEXT_NAME=`kubectl config view -o jsonpath='{.contexts[].name}'`
argocd cluster add $CONTEXT_NAME
```

{{% notice tip %}}
<!--
ArgoCD provides multicluster deployment functionalities. For the purpose of this workshop we will only deploy on the local cluster.
-->
ArgoCDは複数クラスタのデプロイメントに使用できます。このワークショップではローカルクラスタのみにデプロイします。
{{% /notice %}}

<!--
Configure the application and link to your fork (replace the GITHUB_USERNAME):
-->
アプリケーションを設定し、自身のフォークにリンクさせます(GITHUB_USERNAMEを変更してください):
```
kubectl create namespace ecsdemo-nodejs
argocd app create ecsdemo-nodejs --repo https://github.com/GITHUB_USERNAME/ecsdemo-nodejs.git --path kubernetes --dest-server https://kubernetes.default.svc --dest-namespace ecsdemo-nodejs
```

<!--
Application is now setup, let's have a look at the deployed application state:
-->
アプリケーションができました、デプロイされたアプリケーションの状態を確認しましょう:
```
argocd app get ecsdemo-nodejs
```

<!--
You should have this output:
-->
このような表示が出るはずです:
```
Health Status:      Missing

GROUP       KIND              NAMESPACE         NAME              STATUS     HEALTH   HOOK  MESSAGE
_           Service           ecsdemo-nodejs    ecsdemo-nodejs    OutOfSync  Missing        
apps        Deployment        default           ecsdemo-nodejs    OutOfSync  Missing        
```

<!--
We can see that the application is in an `OutOfSync` status since the application has not been deployed yet. 
We are now going to `sync` our application:
-->
アプリケーションはまだデプロイされていないので、 `OutOfSync` になっていることが見えます。
このアプリケーションを `sync` させましょう:
```
argocd app sync ecsdemo-nodejs
```

<!--
After a couple of minutes our application should be synchronized.
-->
数分後にはアプリケーションが同期されているはずです。
```
GROUP  KIND        NAMESPACE       NAME            STATUS  HEALTH   HOOK  MESSAGE
_      Service     ecsdemo-nodejs  ecsdemo-nodejs  Synced  Healthy        service/ecsdemo-nodejs created
apps   Deployment  default         ecsdemo-nodejs  Synced  Healthy        deployment.apps/ecsdemo-nodejs created
```

