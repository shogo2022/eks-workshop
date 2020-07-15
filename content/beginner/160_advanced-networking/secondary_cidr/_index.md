---
title: "EKSでセカンダリCIDRを使う"
chapter: true
weight: 10
---

<!--
# Using Secondary CIDRs with EKS
-->
# EKSでセカンダリCIDRを使う

<!--
You can expand your VPC network by adding additional CIDR ranges. This capability can be used if you are running out of IP ranges within your existing VPC or if you have consumed all available RFC 1918 CIDR ranges within your corporate network. EKS supports additional IPv4 CIDR blocks in the 100.64.0.0/10 and 198.19.0.0/16 ranges. You can review this announcement from our [what's new blog](https://aws.amazon.com/about-aws/whats-new/2018/10/amazon-eks-now-supports-additional-vpc-cidr-blocks/)
-->
追加のCIDRを割り当てることで、VPCネットワークを拡張することができます。これは既存のVPCのIPレンジを使い果たした時や、社内のネットワークでRFC1918のCIDRレンジを使ってしまった時などに使えます。EKSは追加で100.64.0.0/10と198.19.0.0/16のIPv4 CIDRをサポートしています。[what's new blog](https://aws.amazon.com/about-aws/whats-new/2018/10/amazon-eks-now-supports-additional-vpc-cidr-blocks/)でアナウンスを確認できます。

<!--
In this tutorial, we will walk you through the configuration that is needed so that you can launch your Pod networking on top of secondary CIDRs
-->
このチュートリアルでは、セカンダリCIDRでpodを起動する方法をガイドしていきます。