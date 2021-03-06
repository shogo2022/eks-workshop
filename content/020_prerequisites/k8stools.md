---
title: "Kubernetesツールのインストール"
chapter: false
weight: 15
---

<!--
Amazon EKS clusters require kubectl and kubelet binaries and the aws-cli or aws-iam-authenticator
binary to allow IAM authentication for your Kubernetes cluster.
-->
Amazon EKSクラスターはkubectlとkubeletバイナリー、そしてKubernetesクラスターのIAM認証のためにawscliもしくはaws-iam-authenticatorバイナリーが必要です。

<!--
{{% notice tip %}}
In this workshop we will give you the commands to download the Linux
binaries. If you are running Mac OSX / Windows, please [see the official EKS docs
for the download links.](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)
{{% /notice %}}
-->
{{% notice tip %}}
このワークショップではLinuxバイナリーをダウンロードします。
Mac OSX / Windowsを使っている場合は、[公式のEKSドキュメントのダウンロードリンクを確認してください](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)
{{% /notice %}}

<!--
#### Install kubectl

```bash
sudo curl --silent --location -o /usr/local/bin/kubectl \
  https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.7/2020-07-08/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl
```

#### Update awscli
-->
#### kubectlのインストール
```bash
sudo curl --silent --location -o /usr/local/bin/kubectl \
  https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.7/2020-07-08/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl
```
#### awscliのアップデート

<!--
Upgrade AWS CLI according to guidance in [AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html).

```bash
sudo pip install --upgrade awscli && hash -r
```
-->
[AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html)にしたがってAWS CLIをアップデートしてください。

```
sudo pip install --upgrade awscli && hash -r
```

<!--
#### Install jq, envsubst (from GNU gettext utilities) and bash-completion

```bash
sudo yum -y install jq gettext bash-completion moreutils
```
-->
#### jq、 envsubst (GNU gettext utilities)およびbash-completionのインストール
```bash
sudo yum -y install jq gettext bash-completion moreutils
```

<!--
#### Install yq for yaml processing
-->
#### yaml処理用にyqのインストール

```bash
echo 'yq() {
  docker run --rm -i -v "${PWD}":/workdir mikefarah/yq yq "$@"
}' | tee -a ~/.bashrc && source ~/.bashrc
```

<!--
#### Verify the binaries are in the path and executable

```bash
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```
-->
#### バイナリーがpathにあり、実行可能であることを確認します
```
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```

<!--
#### Enable kubectl bash_completion

```bash
kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```
-->
#### kubectlのbash_completionを有効にする
```
kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

<!--
#### set the AWS ALB Ingress Controller version
-->
#### AWS ALB Ingress Controllerのバージョンを設定する

```bash
echo 'export ALB_INGRESS_VERSION="v1.1.8"' >>  ~/.bash_profile
.  ~/.bash_profile
```
