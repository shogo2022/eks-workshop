---
title: "TSCEのCalico拡張のガイド"
weight: 50
draft: true
---
<!--
Now that TSCE is installed in your cluster, you could go back and re-run the examples in the Project Calico section, just to convince yourself that all those features remain working in a TSCE environment.
-->
クラスタにTSCEがインストールされたので、TSCE環境でも全ての機能が動いていることを確認するため、Project Calicoのセクションに戻って、再度動きを確認しても良いでしょう。

<!--
The two feature areas that we are going to showcase in this section are:
-->
このセクションでお見せする機能は次の2つです:

<!--
* [VPC Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) integration.
* Enhanced flow visibility in [CloudWatch](https://aws.amazon.com/cloudwatch/)
-->
* [VPC Security Group](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)との連携。
* より強化された[CloudWatch](https://aws.amazon.com/cloudwatch/)のフロー可視化

<!--
Let's get started.
-->
では始めましょう。