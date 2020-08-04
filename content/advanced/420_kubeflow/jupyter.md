---
title: "Jupyterノートブック"
date: 2019-08-26T00:00:00-08:00
weight: 30
draft: false
---

<!--
### Jupyter Notebook using Kubeflow on Amazon EKS
-->
### Amazon EKS上でKubeflowを使ったJupyterノートブック

<!--
[The Jupyter Notebook](https://jupyter.org/) is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and narrative text. It is often used for data cleaning and transformation, numerical simulation, statistical modeling, data visualization, machine learning, and more.
-->
[Jupyter Notebook](https://jupyter.org/)はオープンソースのwebアプリケーションで、コードや数式、図や説明文などを含む文書を作成、共有することができます。これは、データクリーニングや変形、数式演算、統計モデリング、データ可視化、機械学習などでよく使われています。

<!--
In Kubeflow dashboard, click on **Create a new Notebook server**:
-->
Kubeflowダッシュボードで、 **Create a new Notebook server** をクリックします:

![dashboard](/images/kubeflow/dashboard-new-notebook-server.png)

<!--
Select the namespace created in previous step:
-->
前の手順で作成した名前空間を選択します:

![dashboard](/images/kubeflow/jupyter-select-namespace.png)

<!--
This pre-populates the namespace field on the dashboard. Specify a name **myjupyter** for the notebook:
-->
名前空間はすでに記入されているので、notebookの名前に **myjupyter** と入力します:

![dashboard](/images/kubeflow/jupyter-enter-notebook-server-name.png)

<!--
In the Image section, select the latest **tensorflow-1.x** image whose name ends
in **cpu** (not gpu) from the dropbown box:
-->
Imageセクションでは、ドロップダウンから **tensorflow-1.x** というイメージのうち、名前が **cpu** (gpuではなく)で終わる最新のものを選択します:

![dashboard](/images/kubeflow/jupyter-select-image.png)

<!--
Change the CPU value to **1.0**:
-->
CPUの値を **1.0** に変更します:

![dashboard](/images/kubeflow/jupyter-select-cpu.png)

<!--
Scroll to the bottom, take all other defaults, and click on **LAUNCH**.
-->
他はデフォルトのまま、下までスクロールして **LAUNCH** をクリックします。

<!--
It takes a few seconds for the Jupyter notebook to come online. Click on **CONNECT**
-->
Jupyter notebookがオンラインになるまでには数秒かかります。 **CONNECT** をクリックします

![dashboard](/images/kubeflow/jupyter-notebook-servers.png)

<!--
This connects to the notebook and opens the notebook interface in a new browser tab.
-->
新しいブラウザタブでnotebookが開きます。

![dashboard](/images/kubeflow/jupyter-new-notebook.png)

<!--
CLick on **New**, select **Python3**
-->
**New** をクリックし、 **Python3** を選択します

![dashboard](/images/kubeflow/jupyter-new-python3-notebook.png)

<!--
This creates an empty Python 3 Jupyter notebook
-->
空のPython 3 Jupyter notebookが作成されます

![dashboard](/images/kubeflow/jupyter-empty-notebook.png)

<!--
Copy the [sample training code](/advanced/420_kubeflow/kubeflow.files/mnist-tensorflow-jupyter.py) and paste it in the first code block. This Python sample code uses TensorFlow to create a training model for MNIST database. Click on **Run** to load this code in notebook.
-->
[サンプルのトレーニングコード](/advanced/420_kubeflow/kubeflow.files/mnist-tensorflow-jupyter.py)をコピーして、最初のコードブロックに貼り付けてください。このPythonのサンプルコードはTensorFlowを使ってMNISTデータベースのトレーニングモデルを作成します。 **Run** をクリックしてこのコードをnotebookに読み込ませます。

![dashboard](/images/kubeflow/jupyter-mnist-code.png)

<!--
This also creates a new code block. Copy `main()` in this new code block and click on **Run** again
-->
新しいコードブロックが作成されるので、 `main()` をコピーして、再び **Run** をクリックします

![dashboard](/images/kubeflow/jupyter-mnist-code-main.png)

<!--
This starts the model training and the output is shown on the notebook:
-->
モデルのトレーニングが始まり、notebookに出力されます:

![dashboard](/images/kubeflow/jupyter-mnist-training.png)

<!--
The first few lines shows that TensorFlow and Keras dataset is downloaded. Training data set is 60k images and test data set is 10k images. Hyperparameters used for the training, outputs from five epochs, and finally the model accuracy is shown.
-->
最初の数行はTensorFlowとKerasデータセットがダウンロードされていることを表しています。トレーニングデータセットは60,000の画像で、テストデータセットは10,000の画像です。トレーニングに使われたハイパーパラメータ、5回のエポックそれぞれの出力、そしてモデルの正確性が表示されます。