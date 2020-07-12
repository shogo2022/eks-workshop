---
title: "TSCEを使ってVPCセキュリティグループとKubernetesのネットワークポリシーを連携"
weight: 100
---

<!--
The network security that Calico provides in EKS is great, however it is primarily focused on the EKS cluster itself.  A common use-case for EKS, however, is to build a kubernetes cluster that can interact with other Amazon hosted resources, such as EC2 and RDS instances.  The native protection for those resources is the VPC's Security Group filtering.
-->
CalicoがEKSでできるネットワークセキュリティは素晴らしいが、基本的にEKSクラスタに限られています。しかしながらEKSでは、EC2やRDSインスタンスなどの他のAmazonリソースと相互接続するkubernetesクラスタというのがよくある使い方です。これらのリソースを保護しているのは、VPCのセキュリティグループです。

<!--
The problem with this, however, is that, by default, VPC Security Groups can only be applied to EC2 instances.  Therefore, if you wanted to allow some subset of your pods access to an RDS instance, for example, you would have to allow that access from all of your EKS worker nodes, thereby allowing ALL your EKS pods access to that RDS instance.  That's probably not what you want.  Luckily, one of the capabilities that TSCE enables is the integration of the VPC Security Group mechanism and Kubernetes/Calico network policy.
-->
ここでの問題は、デフォルトではVPCセキュリティグループはEC2インスタンスにのみしか適用できないと言うことです。したがって、決められたサブネットのpodからRDSインスタンスへの通信を許可しようとすると、全てのEKSワーカーノードを許可するすることとなり、結果的にすべてのEKS podがRDSインスタンスにアクセスできることになります。これは望ましくない動きです。幸いなことにTSCEを使えば、VPCのセキュリティグループとKubernetes/Calicoネットワークポリシーの連携が可能です。

<!--
Let's see how that works...
-->
では実際にみてみましょう...

<!--
# Create an EC2 resource in your VPC
-->
# VPC内にEC2リソースの作成

<!--
We're going to create a simple static webserver in your VPC, but not in your EKS cluster to act as a target to demonstrate how pods can become members of a VPC Security Group, and be referenced by VPC Security Group (VSG) policies.
-->
VPCに静的な簡易WEBサーバを作っていきますが、EKSクラスタではなく、VPCセキュリティグループ(VSG)のメンバーになったpodからのアクセス先として作成します。

<!--
To do this, we need a simple web server in the same VPC as your EKS cluster, but running as a separate EC2 instance, not in the EKS cluster itself.  You can do this anyway you like, but if you want some hints, here are some steps you can take to accomplish this.
-->
EKSクラスタと同じVPCに静的な簡易WEBサーバを、EKSクラスタではなくEC2インスタンスで実行します。どのようにやっても良いですが、ヒントとして以下の手順を使ってください。

<!--
* Go into your EC2 console and create an EC2 instances
  * A t2-micro is more than sufficient
  * Do not forget to associate a public IP adress for simplicity
  * Keep EBS settings as default
  * Tag it so you can easily find it later
  * Set up a security group for the instance and allow inbound TCP:80 and TCP:22 from 0.0.0.0/0. Call the security group `protect-sa-sg`
  * Use the `eksworkshop` SSH key you imported earlier
  * Launch and make a note of public and private ip addresses of the instance
  * Ssh into the instance either via Cloud9 by running `ssh ec2-user@<public-ip> -i ~/.ssh/id_rsa` or via the AWS Console by clicking 'Connect'->'EC2 Instance Connect`
  * Run the following commands to install httpd and create a basic web page:
-->
* EC2コンソールでEC2インスタンスの作成
  * t2-microインスタンスで十分
  * シンプルにするためにpublic IPアドレスをつける
  * EBS設定はデフォルトのまま
  * 後で見つけやすいようにタグをつける
  * TCP:80とTC:22を0.0.0.0/0から許可するようにセキュリティグループを設定する。名前は `protect-sa-sg` にする
  * SSH鍵は前のステップで使った `eksworkshop` を使う
  * 起動してインスタンスのパブリックIPとプライベートIPを記録する
  * インスタンスにCloud9から `ssh ec2-user@<public-ip> -i ~/.ssh/id_rsa` でSSH接続するか、AWSコンソールから 'Connect'->'EC2 Instance Connect` で接続する
  * 次のコマンドを実行し、httpdをインストールし、WEBページを作成する:
  
  ```
  sudo yum update -y
  sudo yum install httpd -y
  sudo service httpd start
  sudo chkconfig httpd on
  echo "<html><body>Welcome to Setec Astronomy</body></html>" | sudo tee /var/www/html/index.html
  ```
<!--
# Is the website reachable?
-->
# WEBサイトは見えましたか?

<!--
Let's launch a busybox pod in the cluster and do a curl to the IP address of the EC2 instance we just created.  So, in the cloud9 shell, do the following:
-->
クラスタにbusybox podを立ち上げ、今作ったEC2インスタンスのIPアドレスにcurlをしてみましょう。Cloud9　shellで次を実行します:

```
kubectl run -it test1 --image=busybox -- sh
wget -O - http://<your_ec2_instance_private_IP_here>
```

<!--
You should see your text.  Now exit from the busybox container, but note the instructions on how to re-attach to it, we'll need that later.
-->
テキストが見えるはずです。busyboxから抜けますが、後からまた入るので入り方をメモしておきます。

```
exit
Session ended, resume using 'kubectl attach test1-cd46f75fd-fts4r -c test1 -i -t' command when the pod is running
```

<!--
Next, let's repeat the same again, only this time with test2:
-->
次に、同じことを繰り返しますが、今度はtest2から実行します:

```
kubectl run -it test2 --image=busybox -- sh
wget -O - http://<your_ec2_instance_private_IP_here>
exit
Session ended, resume using 'kubectl attach test2-766c48655b-hr8zj -c test2 -i -t' command when the pod is running
```

<!--
# Tighten up the Security Groups
-->
# セキュリティグループを厳しくする

<!--
First of all, let's create a new security group in the VPC, called `allow-sa-sg`.
-->
まずはじめに、VPCの中に新しいセキュリティグループ `allow-sa-sg` を作成します。

<!--
Next, change the `protect-sa-sg` security group to only allow inbound TCP traffic for port 80 from the `allow-sa-sg` security group members.
-->
次に、 `protect-sa-sg` セキュリティグループを変更して、 `allow-sa-sg` セキュリティグループのメンバーからのTCPポート80番からのみを受け入れるようにします。

<!--
Once that is done, get the Security Group identifiers (*i.e.* sg-xxxxxxxxxxx) for the `allow-sa-sg` security group.  You can do this by listing the VPC's security groups in the VPC console.
-->
完了したら、 `allow-sa-sg` セキュリティグループの識別番号(sg-xxxxxxxxxxx *のような* )を取得します。VPCコンソールでVPCセキュリティグループを一覧することで見ることができます。

![](/images/sg-list.png)

<!--
Now annotate the `test1` pod with the `allow-sa-sg` security group.
-->
`test1` podに `allow-sa-sg` セキュリティグループのannotateをつけます。

```
kubectl annotate pod <test1 pod name> aws.tigera.io/security-groups='["sg-xxxxxx"]'
```

<!--
Lastly, use the instructions provided for connecting back into the *test1* and *test2* pods and re-run the wget commands.  
-->
最後に、 *test1* と *test2* podからサイドwgetコマンドを実行します。

<!--
You should see that *test1* can connect to the webserver, but *test2* is denied.
-->
*test1* からはWEBサーバに接続でき、 *test2* からは拒否されるはずです。

<!--
We have now protected the VPC resource on a per-pod basis using VPC security groups.
-->
これでVPCセキュリティグループを使ってVPCリソースへのpod毎のアクセス制御ができました。