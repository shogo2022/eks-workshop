---
title: "リリースアップデート"
date: 2018-10-087T08:30:11-07:00
weight: 15
draft: false
---
<!--
#### Update Our Application
-->
#### アプリケーションのアップデート

<!--
So far we have walked through setting up CI/CD for EKS using AWS CodePipeline and now we are going to
make a change to the GitHub repository so that we can see a new release built and delivered.
-->
ここまでAWS CodePipelineを使ったEKSのCI/CD設定を見てきたので、ここでGithubレポジトリに変更を加えて新しいリリースがビルドされデプロイされるのを見ていきましょう。

<!--
Open [GitHub](https://github.com/) and select the forked repository with the name **eks-workshop-sample-api-service-go**.
-->
[GitHub](https://github.com/)を開き、フォークした **eks-workshop-sample-api-service-go** のレポジトリを開きます。

<!--
Click on **main.go** file and then click on the **edit** button, which looks like a pencil.
-->
 **main.go** をクリックし、鉛筆のような **edit** ボタンをクリックします。

![GitHub Edit](/images/codepipeline/github_edit.png)

<!--
Change the text where it says "Hello World", add a commit message and then click the "Commit changes" button.
-->
"Hello World"という文字列を変更し、コミットメッセージを入れたら、"Commit changes"ボタンをクリックします。

<!--
You should leave the master branch selected.
-->
ブランチはmasterのままにしておきます。

<!--
{{% notice info %}}
The main.go application needs to be compiled, so please ensure that you don't accidentally break the build :)
{{% /notice %}}
-->
{{% notice info %}}
main.goアプリケーションはコンパイルされる必要があるので、間違ってビルドが壊れないように注意してください :)
{{% /notice %}}

![GitHub Modify](/images/codepipeline/github_modify.png)

<!--
After you modify and commit your change in GitHub, in approximately one minute you will see a new build triggered in the [AWS Management Console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)
![CodePipeline Running](/images/codepipeline/codepipeline_building.png)
-->
Githubでの変更がコミットされたら、大体1分程度で[AWS管理コンソール](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)上に新しいビルドが起動したのが見えます。

<!--
#### Confirm the Change
-->
#### 変更を確認する

<!--
If you still have the ELB URL open in your browser, refresh to confirm the update. If you need to retrieve the URL again, use `kubectl get services hello-k8s -o wide`
-->
まだELBのURLがブラウザ上で開いていたら、更新してアップデートされたことを確認します。URLを確認する必要がある場合は `kubectl get services hello-k8s -o wide` を使います