---
title: "Kubeflowでの分散トレーニング"
date: 2019-11-15T00:00:00-08:00
weight: 90
draft: false
---

<!--
### Distributed Training using tf-operator and pytorch-operator
-->
### tf-operatorとpytorch-operatorを使った分散トレーニング

<!--
`TFJob` is a Kubernetes [custom resource](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) that you can use to run TensorFlow training jobs on Kubernetes. The Kubeflow implementation of TFJob is in [tf-operator](https://github.com/kubeflow/tf-operator). Similarly, you can create PyTorch Job by defining a `PyTorchJob` config file and [pytorch-operator](https://github.com/kubeflow/pytorch-operator) will help create PyTorch job, monitor and keep track of the job.
-->
`TFJob` はKubernetesの[custom resource](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)で、Kubernetes上でTensoFlowトレーニングジョブを実行することができます。KubeflowのTFJobは[tf-operator](https://github.com/kubeflow/tf-operator)で実装されています。同様に、 `PyTorchJob` 設定ファイルを定義することで、[pytorch-operator](https://github.com/kubeflow/pytorch-operator)がPyTorchジョブの作成、監視、追跡をできるようになります。

<!--
Go to your **eks-kubeflow-workshop** notebook server and browse for distributed training notebooks (eks-workshop-notebook/notebooks/03_Distributed_Training/03_01_Distributed_Training_ParameterServer.ipynb). If you haven’t installed notebook server, review [fairing chapter](/advanced/420_kubeflow/fairing/#create-jupyter-notebook-server) and finish the [clone the repo](/advanced/420_kubeflow/fairing/#clone-the-repo) instructions.
-->
 **eks-kubeflow-workshop** notebookサーバで分散トレーニングnotebook(eks-workshop-notebook/notebooks/03_Distributed_Training/03_01_Distributed_Training_ParameterServer.ipynb)を開きます。notebookサーバがインストールされていない場合は、[fairingの章](/advanced/420_kubeflow/fairing/#create-jupyter-notebook-server)を参照し、[レポジトリのクローン](/advanced/420_kubeflow/fairing/#clone-the-repo)を完了させてください。

<!--
You can go over basic concepts of distributed training. In addition, we prepare `distributed-tensorflow-job.yaml` and `distributed-pytorch-job.yaml` for you to run distributed training jobs on EKS. You can follow guidance to check job specs, create the jobs and monitor the jobs.
-->
分散トレーニングの基本概念を進めてください。追加で、 `distributed-tensorflow-job.yaml` と `distributed-pytorch-job.yaml` を用意して、EKS上で分散トレーニングが実行できるようにします。ガイドに従ってジョブの仕様を確認し、ジョブの作成と監視をしてみてください

![dashboard](/images/kubeflow/distributed-training-notebook.jpg)

<!--
{{% notice info %}}
Starting from here, its important to read notebook instructions carefully. The info provided in the workshop is lightweight and you can use it to ensure desired result. You can complete the exercise by staying in the notebook
{{% /notice %}}
-->
{{% notice info %}}
ここからは、特にnotebookのガイドをしっかり読んでください。ワークショップ内で提供されている情報は軽量で、かつ一定の結果が出るようになっています。notebook内でハンズオンは完結します
{{% /notice %}}