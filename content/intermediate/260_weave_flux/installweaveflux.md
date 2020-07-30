---
title: "Weave Fluxのインストール"
date: 2018-10-087T08:30:11-07:00
weight: 15
draft: false
---

<!--
Now we will use Helm to install Weave Flux into our cluster and enable it to interact with our Kubernetes configuration GitHub repo.  
-->
Helmを使ってクラスタにWeave Fluxをインストールし、KubernetesファイルがあるGitHubレポジトリと連携するようにします。

<!--
First, install the Flux Custom Resource Definition:
-->
まずは、FLuxのCustom Resourcce Definitionを適用します:

```
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml
```

<!--
Check that Helm is installed. 
-->
Helmがインストールされたことを確認します。

```
helm list
```

<!--
This command should either return a list of helm charts that have already been deployed or nothing.
-->
デプロイされたhelm chartのリストが返ってくるはずです。

<!--
{{% notice info %}}
If you get an error message, see [installing helm](/beginner/060_helm/helm_intro/install/index.html) for instructions.
{{% /notice %}}
-->
{{% notice info %}}
エラーメッセージが出たら、[helmのインストール](/beginner/060_helm/helm_intro/install/index.html)を参照してください。
{{% /notice %}}

<!--
> In the following steps, your Git user name will be required. Without this information, the resulting pipeline will not function as expected. Set this as an environment variable to reuse in the next commands:
-->
> 次の手順では、自身のGitユーザ名が必要です。この情報はパイプラインが正常に動くために必須です。再利用できるように、次のコマンドで環境変数ん設定しましょう:

```bash
YOURUSER=yourgitusername
```

<!--
First, create the flux Kubernetes namespace
-->
最初に、flux用の名前空間を作成します

```
kubectl create namespace flux
```

<!--
Next, add the Flux chart repository to Helm and install Flux.  
-->
次に、HelmにFlux chartレポジトリを追加し、Fluxをインストールします。

<!--
{{% notice info %}}
Update the Git URL below to match your user name and Kubernetes configuration manifest repository.
{{% /notice %}}
-->
{{% notice info %}}
以下のGit URLを、自身のユーザ名とKubernetesのマニフェストファイルがあるレポジトリにそれぞれ変更してください。
{{% /notice %}}

```
helm repo add fluxcd https://charts.fluxcd.io

helm upgrade -i flux fluxcd/flux \
--set git.url=git@github.com:${YOURUSER}/k8s-config \
--namespace flux

helm upgrade -i helm-operator fluxcd/helm-operator \
--set helm.versions=v3 \
--set git.ssh.secretName=flux-git-deploy \
--namespace flux
```

<!--
Watch the install and confirm everything starts.  There should be 3 pods.  
-->
進捗を観察してすべてが起動したことを確認します。podが3つ作成されているはずです。
```
kubectl get pods -n flux
```

<!--
Install fluxctl in order to get the SSH key to allow GitHub write access.  This allows Flux to keep the configuration in GitHub in sync with the configuration deployed in the cluster.  
-->
GitHubへの書き込み権限を得るために、fluxctlをインストールしてSSH鍵を取得します。これによりFluxはGitHub上の設定とクラスタにデプロイされた設定を同期させることができます。
```
sudo wget -O /usr/local/bin/fluxctl $(curl https://api.github.com/repos/fluxcd/flux/releases/latest | jq -r ".assets[] | select(.name | test(\"linux_amd64\")) | .browser_download_url")
sudo chmod 755 /usr/local/bin/fluxctl

fluxctl version
fluxctl identity --k8s-fwd-ns flux
```

<!--
Copy the provided key and add that as a deploy key in the GitHub repository.  
-->
表示された鍵をコピーして、GitHubレポジトリにdeploy keyとして追加します。

<!--
* In GitHub, select your k8s-config GitHub repo.  Go to **Settings** and click **Deploy Keys**.  Alternatively, you can go by direct URL by replacing your user name in this URL: **github.com/YOURUSER/k8s-config/settings/keys**.  
* Click on **Add Deploy Key**
 * Name: **Flux Deploy Key**
 * Paste the key output from fluxctl
 * Click **Allow Write Access**.  This allows Flux to keep the repo in sync with the real state of the cluster
 * Click **Add Key**
-->
* GitHubへ行き、自身のk8s-config GitHubレポジトリを開きます。 **Settings** で、 **Deploy Keys** をクリックします。もしくは、次のURLのユーザ名を自身のユーザ名に変更すれば直接アクセスできます: **github.com/YOURUSER/k8s-config/settings/keys**.  
* **Add Deploy Key** をクリックします。
 * Name: **Flux Deploy Key**
 * fluxctlで出力された鍵を貼り付け
 * **Allow Write Access** をクリック。これでFluxがクラスタの実状況とレポを同期することが可能になります。
 * **Add Key** をクリック

<!--
Now Flux is configured and should be ready to pull configuration.  
-->
これでFluxの設定が完了したので、設定をpullできるようになったはずです。