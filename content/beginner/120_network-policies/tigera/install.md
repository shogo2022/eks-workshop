---
title: "Tigera Secure Cloud Editionのインストール"
weight: 30
---
<!--
Now that your environment variables are set, and _tsctl_ is installed, we need to install TSCE itself.  To do so, run the following command.  The instructions in the Tigera Secure CE v1.0.1 document that you downloaded earlier are almost exactly the same as what is shown here.  The only difference is that we've changed a variable name from ```$TOKEN``` to ```$TS_TOKEN``` to avoid colliding with other ```$TOKEN``` variables that might be set in your environment.
-->
環境変数が設定され、 _tsctl_ もインストールされたので、次にTSCEをインストールするため、次のコマンドを実行します。この手順は前の手順でダウンロードしたTigera Secure CE v1.0.1のドキュメントとほぼ同じです。唯一の違いは、既存の環境変数とぶつからないように変数の名前を ```$TOKEN``` から ```$TS_TOKEN``` にしています。
```
tsctl install --token $TS_TOKEN \
            --kubeconfig ~/.kube/config \
            --cluster-name $CLUSTER_NAME \
            --vpc-id $VPC_ID \
            --control-plane-sg $CONTROL_PLANE_SG \
            --node-sgs $K8S_NODE_SGS
```

<!--
Copy that text and run it in your Cloud9 shell.  If all goes well, in a few minutes, you should be running TSCE, which augments Project Calico with a number of interesting capabilities which we will explore next.
-->
このテキストをコピーして、Cloud9 shellで実行してください。うまくいけば数分のうちにTSCEが走り、Project Calicoを補完する数々の面白い機能が使えるようになっています。

<!--
### Known Issues
-->
### 既知の問題

<!--
* It may take up to five seconds for pods to gain network connectivity after starting up.
-->
* 起動直後、podがネットワークに通信できるようになるまで最大5秒程度かかることがあります。

<!--
* Network Load Balancers (NLBs) may lose their ability to balance traffic to pods after installing Tigera Secure CE. To resolve this issue, manually modify the pods’ security group to allow ingress traffic from the original source of the traffic (not the NLB). See the User Guide for more information or contact Tigera support for assistance.
-->
* Tigera Secure CEのインストール後、Network Load Balancers (NLBs)がpodに通信を割り振ることができなくなるかもしれません。この問題を解決するには、podのセキュリティグループを手動で変更し、(NLBではなく)発信元からの通信を許可するようにしてください。詳細情報はユーザーガイドもしくはTigeraサポートに聞いてください。