---
title: "Kubeflowを使った機械学習"
chapter: true
weight: 420
draft: false
aliases:
    - /kubeflow/
tags:
  - advanced
  - kubeflow
  - ml
  - OPN401
---

<!--
# Machine Learning using [Kubeflow](https://kubeflow.org/)
-->
# [Kubeflow](https://kubeflow.org/)を使った機械学習

<!--
[Kubeflow](https://kubeflow.org) provides a simple, portable, and scalable way of running Machine Learning workloads on Kubernetes.
-->
[Kubeflow](https://kubeflow.org/)はKubernetesでの機械学習をシンプルでポータブル、そして拡張を容易にします。

<!--
In this module, we will install Kubeflow on Amazon EKS, run a single-node training and inference using [TensorFlow](https://tensorflow.org/), train and deploy model locally and remotely using Fairing, setup Kubeflow pipeline and review how to call AWS managed services such as Sagemaker for training and inference.
-->
このモジュールではAmazon EKSにKubeflowをインストールします。[TensorFlow](https://tensorflow.org/)を動かす単一ノードで、モデルをローカルとリモートにFairingを使って実装します。その後、Kubeflowパイプラインを設定し、SagemakerなどのAWSマネージドサービスの呼び出し方をみていきます。

![Kubeflow Logo](/images/kubeflow/kubeflow.svg)
