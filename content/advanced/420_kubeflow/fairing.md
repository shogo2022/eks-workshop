---
title: "Fairing"
date: 2019-11-13T21:55:32-05:00
weight: 60
draft: false
---
### Kubeflow Fairing

<!--
Jupyter notebooks are a great way to author your model creation. You can write the algorithms, train the model and if you need a way to publish the inference endpoint directly from this interface, you can use Kubeflow fairing to do so
-->
Jupyter notebookはモデルの作成や、アルゴリズムの記述、モデルのトレーニングができる素晴らしい方法です。推論エンドポイントを公開した場合には、Kubeflow fairingを使うことで実現できます。

<!--
#### Assign S3 and ECR permissions
-->
#### S3とECR権限の割り当て

<!--
For this chapter, we will make use of both S3 and ECR services. We'll use S3 to store and access pipeline data. We'll use ECR as our container registry for the training image. We need to add IAM policies to Worker nodes so that we can access both S3 and ECR. Run below commands in Cloud9 and assign desired permission
-->
この章ではS3とECR両サービスを使います。パイプラインデータの保存とアクセスにS3を使い、トレーニングイメージのコンテナレジストリとしてECRを使います。S3とECRにアクセスさせるために、ワーカーノードにIAMポリシーを追加する必要があります。Cloud9で次のコマンドを実行して、必要な権限を割り当てましょう。

```
aws iam attach-role-policy --role-name $ROLE_NAME --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryFullAccess

aws iam attach-role-policy --role-name $ROLE_NAME --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess
```

<!--
#### Create Jupyter notebook server
Create new notebook server by following [Jupyter notebook chapter] (/advanced/420_kubeflow/jupyter). Before you jump to the link, take a note of custom image (**527798164940.dkr.ecr.us-west-2.amazonaws.com/tensorflow-1.15.2-notebook-cpu:1.0.0**) that you will use for **eks-kubeflow-workshop** notebook server. Below screenshot depicts how to use custom image
-->
#### Jupyter notebook サーバの作成
[Jupyter notebookの章] (/advanced/420_kubeflow/jupyter)に沿って新しいnotebookを作成します。リンクに行く前に、 **eks-kubeflow-workshop** のnotebookサーバに使うカスタムイメージ　(**527798164940.dkr.ecr.us-west-2.amazonaws.com/tensorflow-1.15.2-notebook-cpu:1.0.0**) をメモしておいてください。次の図ではカスタムイメージの使い方を示しています。

![dashboard](/images/kubeflow/eks-kubeflow-workshop-notebook-server.png)

<!--
#### Clone the repo
We will clone Github repo from a Jupyter notebook so that we can readily use authored notebooks in this chapter.
-->
#### レポジトリのクローン
Jupyter notebookからGithubのレポをクローンします。

<!--
Create new Python 3 Notebook if one doesn't exist. Run the command below to clone the repo
-->
まだ作っていない場合はPython 3 Notebookを作成します。次のコマンドでレポをクローンします

```
!git clone https://github.com/aws-samples/eks-kubeflow-workshop.git
```
<!--
Click Run. This will clone the repo into your notebook server
-->
Runをクリックすると、自身のnotebookサーバにレポがクローンされます。

<!--
Close the notebook tab, go back to the notebook server, select the notebook that we just used and click **Shutdown**.
-->
notebookタブを閉じたらnotebookサーバに戻り、使っていたnotebookを選択したら、 **Shutdown** をクリックします。

![dashboard](/images/kubeflow/fairing-shutdown-notebook.png)

<!--
#### Run fairing introduction notebook
-->
#### fairingイントロダクションnotebookの実行

<!--
Browse the **"eks-kubeflow-workshop"** repository and go to fairing introduction notebook (eks-kubeflow-workshop/notebooks/02_Fairing/02_01_fairing_introduction.ipynb). You can either click on the notebook to open or select and click **View**
-->
**"eks-kubeflow-workshop"** レポジトリで、fairingのイントロダクションnotebook(eks-kubeflow-workshop/notebooks/02_Fairing/02_01_fairing_introduction.ipynb)へ行きます。notebookをクリックするか、選択して **View** をクリックすると開くことができます

![dashboard](/images/kubeflow/fairing-view-introduction-notebook.png)

<!--
{{% notice info %}}
Starting from here, its important to read notebook instructions carefully. The info provided in the workshop is lightweight and you can use it to ensure desired result. You can complete the exercise by staying in the notebook
{{% /notice %}}
-->
{{% notice info %}}
ここからは、特にnotebookのガイドをしっかり読んでください。ワークショップ内で提供されている情報は軽量で、かつ一定の結果が出るようになっています。notebook内でハンズオンは完結します
{{% /notice %}}

<!--
Review the content and click first cell and click **Run**. This will let you install Fairing from Github repository
-->
内容を確認し、最初のセルをクリックしたら、 **Run** をクリックします。これでGitHubレポジトリからFairingをインストールできます

<!--
Wait till it finishes, go to next cell and click **Run**. Here is expected result
-->
完了を待って、次のセルに移り、 **Run** をクリックします。このようになるはずです
![dashboard](/images/kubeflow/fairing-install-from-github.png)

<!--
Now that we have fairing installed, we will train a model authored in Python. The model will create a linear regression model that allows us to learn a function or relationship from a given set of continuous data. For example, we are given some data points of x and corresponding y and we need to learn the relationship between them that is called a hypothesis.
-->
fairingがインストールされたので、Pythonで書かれたモデルをトレーニングします。これは連続的なデータ内の関係性などを学習し、線型回帰モデルを生成します。例として、ある点xと、対になる点yのデータポイントをいくつか与えられた時に、関係性を仮説として学習させます。

<!--
In case of linear regression, the hypothesis is a straight line i.e, h(x) = x * weight + b.
-->
線型回帰では、仮説は直線です。例、h(x) = x * weight + b

<!--
Run cell 3. Once it completes, run cell 4. This will create a model locally on our notebook
-->
３つめのセルを実行します。完了したら、4つめを実行します。これでモデルがローカルのnotebookに作成されます
![dashboard](/images/kubeflow/fairing-train-locally.png)

<!--
Now, lets use fairing and push the image into ECR which can be used for remote training
-->
では、fairingを使ってイメージをECRにpushしてリモートトレーニングに使えるようにしましょう

<!--
Before you run authenticate with ECR, change the region if needed. Run this cell and login so that you can perform ECR operations
-->
ECRで認証を実行する前に、必要に応じてリージョンを変更してください。このセルを実行すると、ECRでのオペレーションを行うためにログインが行われます

<!--
Run the next cell and create an ECR repository (fairing-job) in the same region. You should see similar output
-->
次のセルを実行してECRレポジトリ(fairing-job)を同じリージョンに作成します。このような表示が見えるはずです
![dashboard](/images/kubeflow/fairing-create-ecr-repo.png)

<!--
Let's run next cell. Fairing pushes the image to ECR and then deploys the model remotely
-->
次のセルを実行しましょう。FairingがイメージをECRにpushしてリモートにモデルをデプロイします。

![dashboard](/images/kubeflow/fairing-remote-job.png)

<!--
Now that we have demonstrated how to use Fairing to train locally and remotely, let's train and deploy XGBoost model and review an end to end implementation
-->
Fairingを使ってのローカルでのトレーニングと、　リモートからのトレーニングを実施しました。XGBoostモデルをトレーニング、デプロイして、一貫した実装をみてみましょう

<!--
#### Run fairing end to end deployment notebook
-->
#### faring エンド・ツー・エンド　デプロイメントのnotebookを実行

<!--
For this exercise, we will use another notebook called **02_06_fairing_e2e.ipynb**
-->
ここでは、 **02_06_fairing_e2e.ipynb** という別のnotebookを使います

<!--
Go back to your notebook server and shutdown 02_01_fairing_introduction.ipynb notebook. Open the **02_06_fairing_e2e.ipynb**
-->
notebookサーバに戻り、02_01_fairing_introduction.ipynb notebookをシャットダウンしたら、 **02_06_fairing_e2e.ipynb** を開きます

<!--
Let's install python dependencies by running first 2 cells, and then run next 2 cells under "Develop your model"
-->
最初の2つのセルを実行してpythonの依存パッケージをインストールします。そして"Develop your model"の下の2つのセルを実行します

<!--
Run the next cell to train the XGBoost model locally. Here is the expected result
-->
次のセルを実行して、ローカルでXGBoostモデルをトレーニングします。このように表示されます
![dashboard](/images/kubeflow/fairing-xgboost-local.png)

<!--
Now lets create an S3 bucket to store pipeline data. Remember to change HASH to a random value before running next cell
-->
パイプラインデータを保存するS3バケットを作成します。次のセルを実行する前に、HASHをランダムな値に変更しておいてください

<!--
Running next two steps will setup Fairing backend. Remember to change S3 bucket name before running next cell
-->
次の2ステップで　Fairingのバックエンドを設定します。実行の前に、S3のバケット名を変更してください

<!--
Now lets submit the Trainjob. Here is the expected result
-->
では、トレーニングジョブを実行しましょう。このような表示になります
![dashboard](/images/kubeflow/fairing-xgboost-remote.png)

<!--
Running next step will deploy prediction endpoint using Fairing. You will endpoint details at the bottom.
-->
次のステップで、Fairingを使って予測エンドポイントをデプロイします。下に、エンドポイント詳細がでます。
![dashboard](/images/kubeflow/fairing-deploy-prediction.png)

<!--
Let's call this prediction endpoint. Remember to replace <endpoint> with your endpoint before running next two cells
-->
この予測エンドポイントを呼び出してみましょう。次の2つのセルを実行する前に<endpoint>を自分自身のエンドポイントに変更してください
![dashboard](/images/kubeflow/fairing-call-prediction.png)

<!--
This demonstrates how to build XGBoost model using Fairing and deploy it locally and to remote endpoint.
-->
このデモでは、Fairingを使ってXGBoostモデルをビルドし、ローカル及びリモートへのデプロイを実施しました。

<!--
Run the next steps to cleanup resources from this exercise
-->
次のステップでは、このハンズオンのアタ片付けを行います