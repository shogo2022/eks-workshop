---
title: "CodePipelineでのイメージ作成"
date: 2018-10-087T08:30:11-07:00
weight: 20
draft: false
---

<!--
Now we are going to create the AWS CodePipeline using [AWS CloudFormation](https://aws.amazon.com/cloudformation/).  This pipeline will be used to build a Docker image from your GitHub source repo (eks-example).  Note that this does not deploy the image.  Weave Flux will handle that.  
-->
では[AWS CloudFormation](https://aws.amazon.com/cloudformation/)を使ってAWS CodePipelineを作っていきます。このパイプラインはあなた自身のGitHubレポ(eks-example)からDockerイメージをビルドするのに使われます。これ自体はイメージ位のデプロイは行わず、Weave Fluxが行うことに注意してください。

<!--
CloudFormation is an [infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_Code) (IaC) tool which
provides a common language for you to describe and provision all the infrastructure resources in your cloud environment.
CloudFormation allows you to use a simple text file to model and provision, in an automated and secure manner, all the
resources needed for your applications across all regions and accounts.
-->
CloudFormationは[infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_Code) (IaC)ツールで、クラウド環境の全てのリソースを宣言し、構築できる共通言語を提供します。
CloudFormationはシンプルなテキストファイルを使い、全てのリージョンとアカウントにまたがる全てのリソースを、自動かつ安全な方法で形作り、構築することを可能にします。

<!--
Each EKS deployment/service should have its own CodePipeline and be located in an isolated source repository.  
-->
それぞれのEKS deployment/serviceは、自身のCodePipelineを持ち、分離されたソースレポジトリに配置さ荒れているべきです。

<!--
Click the **Launch** button to create the CloudFormation stack in the AWS Management Console.
-->
AWS管理コンソールで **Launch** ボタンをクリックして、CloudFormationスタックを作ります。

| Launch template |  |  |
| ------ |:------:|:--------:|
| CodePipeline & EKS |  {{< cf-launch "weave_flux_pipeline.cfn.yml" "image-codepipeline" >}} | {{< cf-download "weave_flux_pipeline.cfn.yml" >}}  |

<!--
After the console is open, enter your GitHub username, personal access token (created in previous step) and then click the "Create stack" button located at the bottom of the page.
-->
コンソールが開いたら、GitHubうユーザ名と(先の手順で作った)personal access tokenを入力し、ページ下の"Create stack"ボタンをクリックします。

![CloudFormation Stack](/images/weave_flux/cloudformation_stack.png)

<!--
Wait for the status to change from "CREATE_IN_PROGRESS" to **CREATE_COMPLETE** before moving on to the next step.
-->
次の手順に進む前に、ステータスが"CREATE_IN_PROGRESS"から **CREATE_COMPLETE** に変わるのを待ちます。

![CloudFormation Stack](/images/weave_flux/cloudformation_stack_creating.png)

<!--
Open [CodePipeline in the Management Console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines). You will see a CodePipeline that starts with **image-codepipeline**.
Click this link to view the details.
-->
[管理コンソールでCodePipeline](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)を開きます。 **image-codepipeline** で始まるCodePipelineが見えるので、クリックして詳細を確認します。

<!--
{{% notice tip %}}
If you receive a permissions error similar to **User x is not authorized to perform: codepipeline:ListPipelines...** upon clicking the above link, the CodePipeline console may have opened up in the wrong region.  To correct this, from the **Region** dropdown in the console, choose the region you provisioned the workshop in. 
{{% /notice %}}
-->
{{% notice tip %}}
リンクをクリックした時に **User x is not authorized to perform: codepipeline:ListPipelines...** というエラーが出た場合は、CodePipelineコンソールが誤ったリージョンで開いているかもしれません。これを正すには、コンソールの **Region** ドロップダウンからworkshopを作成したリージョンを選んでください。
{{% /notice %}}

<!--
Currently the image build is likely failed because we have no code in our repository.  We will add a sample application to our GitHub repo (eks-example).  Clone the repo substituting your GitHub user name.  
-->
現時点ではレポジトリの中にコードがないので、イメージビルドは失敗します。GitHubレポ(eks-example)にサンプルのアプリケーションを追加します。あなた自身のGitHubユーザ名に変えて、クローンします。
```
git clone https://github.com/${YOURUSER}/eks-example.git
cd eks-example
```

<!--
Next create a base README file, a source directory, and download a sample nginx configuration (hello.conf), home page (index.html), and Dockerfile. 
-->
次は基本のREADMEファイル、ソースディレクトリを作成し、サンプルのnginx設定ファイル(hello.conf)、ホームページファイル(index.html)、そしてDockerfileをダウンロードします。

```
echo "# eks-example" > README.md
mkdir src
wget -O src/hello.conf https://raw.githubusercontent.com/aws-samples/eks-workshop/main/content/intermediate/260_weave_flux/app.files/hello.conf
wget -O src/index.html https://raw.githubusercontent.com/aws-samples/eks-workshop/main/content/intermediate/260_weave_flux/app.files/index.html
wget https://raw.githubusercontent.com/aws-samples/eks-workshop/main/content/intermediate/260_weave_flux/app.files/Dockerfile
```

<!--
Now that we have a simple hello world app, commit the changes to start the image build pipeline.  
-->
これでシンプルなhello worldアプリができたので、変更をコミットしてイメージビルドのパイプラインを起動します。

```
git add .
git commit -am "Initial commit"
git push 
```

<!--
In the [CodePipeline console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines) go to the details page for the specific CodePipeline.  You can see status along with links to the change and build details. 
-->
[CodePipelineコンソール](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)で、指定のCodePipelineの詳細ページに行きます。ステータスと一緒に、変更とビルド詳細へのリンクが見えます。

![CodePipeline Details](/images/weave_flux/codepipeline_details.png)

<!--
{{% notice tip %}}
If you click on the "details" link in the build/deploy stage, you can see the output from the CodeBuild process.
{{% /notice %}}
-->
{{% notice tip %}}
build/deployステージの"details"リンクをクリックすると、CodeBuildプロセスの出力を見ることができます。
{{% /notice %}}

<!--
To verify the image is built, go to the [Amazon ECR console](https://console.aws.amazon.com/ecr/repositories) and look for your **eks-example** image repository.  
-->
イメージがビルドされたことを確認するために、[Amazon ECR コンソール](https://console.aws.amazon.com/ecr/repositories)に行き、 **eks-example** イメージレポジトリを確認します。
