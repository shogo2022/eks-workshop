---
title: "Kubernetesツールのインストール"
chapter: false
weight: 15
---

<!--
Amazon EKS clusters require kubectl and kubelet binaries and the aws-cli or aws-iam-authenticator
binary to allow IAM authentication for your Kubernetes cluster.
-->
Amazon EKSクラスターはkubectlとkubeletバイナリーそしてawscliもしくはaws-iam-authenticatorバイナリーが
KubernetesクラスターのIAM認証のために必要です。

<!--
{{% notice tip %}}
In this workshop we will give you the commands to download the Linux
binaries. If you are running Mac OSX / Windows, please [see the official EKS docs
for the download links.](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)
{{% /notice %}}
-->
{{% notice tip %}}
このワークショップではLinuxバイナリーをダウンロードするコマンドを伝えます。
Mac OSX / Windowsを使っている場合は、[公式のEKSドキュメントのダウンロードリンクを確認してください](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)
{{% /notice %}}

<!--
#### Install kubectl
```
sudo curl --silent --location -o /usr/local/bin/kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/kubectl
sudo chmod +x /usr/local/bin/kubectl
```
#### Update awscli
-->
#### kubectlのインストール
```
sudo curl --silent --location -o /usr/local/bin/kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.15.10/2020-02-22/bin/linux/amd64/kubectl
sudo chmod +x /usr/local/bin/kubectl
```
#### awscliのアップデート

<!--
Upgrade AWS CLI according to guidance in [AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html).

```
sudo pip install --upgrade awscli && hash -r
```
-->
[AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html)にしたがってAWS CLIをアップデートしてください。

```
sudo pip install --upgrade awscli && hash -r
```

<!--
#### Install jq, envsubst (from GNU gettext utilities) and bash-completion
```
sudo yum -y install jq gettext bash-completion moreutils
```
-->
#### jq、 envsubst (GNU gettext utilities)およびbash-completionのインストール
```
sudo yum -y install jq gettext bash-completion moreutils
```

<!--
#### Install yq for yaml processing
-->
#### yaml処理用にyqのインストール

```
echo 'yq() {
  docker run --rm -i -v "${PWD}":/workdir mikefarah/yq yq "$@"
}' | tee -a ~/.bashrc && source ~/.bashrc
```

<!--
#### Verify the binaries are in the path and executable
```
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```
-->
#### バイナリーがpathにあり、実行可能であること
```
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```

<!--
#### Enable kubectl bash_completion
```
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