---
title: "モデル推論"
date: 2019-08-27T00:00:00-08:00
weight: 50
draft: false
---

<!--
### Model Inference
-->
### モデル推論

<!--
After the model is trained and stored in S3 bucket, the next step is to use that model for inference.
-->
モデルがトレーニングされ、S3バケットに保存された後は、モデルを使って推論をします。

<!--
This chapter explains how to use the previously trained model and run inference using TensorFlow and Keras on Amazon EKS.
-->
この章は　前の手順でトレーニングされたモデルを使って、Amazon EKS上でTensorFlowとKerasを使って推論をする方法を学びます。

<!--
#### Run inference pod
-->
#### 推論podを実行

<!--
A model from training was stored in the S3 bucket in previous section. Make sure `S3_BUCKET` and `AWS_REGION` environment variables are set correctly.
-->
トレーニングされたモデルはS3バケットに保存されています。 `S3_BUCKET` と `AWS_REGION` の環境変数が正しくセットされているかを確認します。

```
curl -LO https://eksworkshop.com/advanced/420_kubeflow/kubeflow.files/mnist-inference.yaml
envsubst <mnist-inference.yaml | kubectl apply -f -
```

<!--
Wait for the containers to start and run the next command to check its status
-->
コンテナが起動するのを待って、ステータス確認の次のコマンドを実行します

```
kubectl get pods -l app=mnist,type=inference
```
<!--
You should see similar output
-->
このような表示が見えるはずです
```
NAME                    READY   STATUS      RESTARTS   AGE
mnist-96fb6f577-k8pm6   1/1     Running     0          116s
```

<!--
Now, we are going to use Kubernetes port forward for the inference endpoint to do local testing:
-->
推論エンドポイントをローカルでテストするために、Kubernetesのport forwardを使います。

```
kubectl port-forward `kubectl get pods -l=app=mnist,type=inference -o jsonpath='{.items[0].metadata.name}' --field-selector=status.phase=Running` 8500:8500
```

<!--
Leave the current terminal running and open a new terminal for installing tensorflow
-->
現在のターミナルはそのままで、tensorflowをインストールするために新しいターミナルを開きます

<!--
#### Install packages
-->
#### パッケージのインストール

<!--
Install `tensorflow` package:
-->
`tensorflow` パッケージのインストール

```
curl -O https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py --user
pip3 install tensorflow --user
```

<!--
#### Run inference
-->
#### 推論の実行

<!--
Use the script [inference_client.py](/advanced/420_kubeflow/kubeflow.files/inference_client.py) to make prediction request. It will randomly pick one image from test dataset and make prediction.
-->
[inference_client.py](/advanced/420_kubeflow/kubeflow.files/inference_client.py)のスクリプトを使い、予測のリクエストをします。テストデータセットからランダムに一つ画像が選ばれ、予測が行われます。

```
curl -LO https://eksworkshop.com/advanced/420_kubeflow/kubeflow.files/inference_client.py
python inference_client.py --endpoint http://localhost:8500/v1/models/mnist:predict
```

<!--
It will randomly pick one image from test dataset and make prediction.
-->
テストデータセットからランダムに一つ画像が選ばれ、予測が行われます。

{{< output >}}
Data: {"instances": [[[[0.0], [0.0], [0.0], [0.0], [0.0] ... 0.0], [0.0]]]], "signature_name": "serving_default"}
The model thought this was a Ankle boot (class 9), and it was actually a Ankle boot (class 9)
{{< /output >}}
