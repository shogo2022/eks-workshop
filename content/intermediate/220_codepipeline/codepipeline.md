---
title: "CodePipelineの設定"
date: 2018-10-087T08:30:11-07:00
weight: 14
draft: false
---

<!--
Now we are going to create the AWS CodePipeline using [AWS CloudFormation](https://aws.amazon.com/cloudformation/).
-->
では、[AWS CloudFormation](https://aws.amazon.com/cloudformation/)を使ってAWS CodePipelineを作成します。

<!--
CloudFormation is an [infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_Code) (IaC) tool which
provides a common language for you to describe and provision all the infrastructure resources in your cloud environment.
CloudFormation allows you to use a simple text file to model and provision, in an automated and secure manner, all the
resources needed for your applications across all regions and accounts.
-->
CloudFormationは、クラウド環境のインフラリソースを宣言的に構築するための共通言語を提供する[infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_Code) (IaC) のツールです。CloudFomrationはシンプルなテキストファイルで全てのリージョンとアカウントに跨って必要なリソースを、自動的かつセキュアに形作り設定できるようにしてくれます。

<!--
Each EKS deployment/service should have its own CodePipeline and be located in an isolated source repository.
-->
それぞれのEKS deployment/serviceは、それぞれのCodePipelineがあり、分離されたソースリポジトリにあるべきです。

<!--
You can modify the CloudFormation templates provided with this workshop to meet your system requirements to easily
onboard new services to your EKS cluster. For each new service the following steps can be repeated.
-->
自身の要件にあわせ、新しいサービスを簡単にデプロイできるように、このワークショップで提供されたCloudFormationのテンプレートを変更してください。新規サービスごとに次の手順を繰り返してください。

<!--
Click the **Launch** button to create the CloudFormation stack in the AWS Management Console.
-->
AWS管理コンソールで **Launch** ボタンを押して、CloudFormationスタックを作成します。

| Launch template |  |  |
| ------ |:------:|:--------:|
| CodePipeline & EKS |  {{< cf-launch "ci-cd-codepipeline.cfn.yml" "eksws-codepipeline" >}} | {{< cf-download "ci-cd-codepipeline.cfn.yml" >}}  |

<!--
After the console is open, enter your GitHub username, personal access token (created in previous step), check the acknowledge box and then click the "Create stack" button located at the bottom of the page.
-->
コンソールが開いたら、Githubユーザ名と(前の手順で作成した)パーソナルアクセストークンを入力し、acknowledgeをチェックしたら"Create stack"をクリックします。

![CloudFormation Stack](/images/codepipeline/cloudformation_stack.png)

<!--
Wait for the status to change from "CREATE_IN_PROGRESS" to **CREATE_COMPLETE** before moving on to the next step.
-->
次の手順に進む前に、ステータスが"CREATE_IN_PROGRESS"から **CREATE_COMPLETE** になるのを待ちます。

![CloudFormation Stack](/images/codepipeline/cloudformation_stack_creating.png)

<!--
Open [CodePipeline in the Management Console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines). You will see a CodePipeline that starts with **eks-workshop-codepipeline**.
Click this link to view the details.
-->
[管理コンソールでCodePipeline](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)を開きます。 **eks-workshop-codepipeline** で始まるCodePipelineが見えるので、リンクをクリックして詳細をみます。

<!--
{{% notice tip %}}
If you receive a permissions error similar to **User x is not authorized to perform: codepipeline:ListPipelines...** upon clicking the above link, the CodePipeline console may have opened up in the wrong region.  To correct this, from the **Region** dropdown in the console, choose the region you provisioned the workshop in.  Select Oregon (us-west-2) if you provisioned the workshow per the "Start the workshop at an AWS event" instructions.
{{% /notice %}}
-->
{{% notice tip %}}
リンクをクリックして **User x is not authorized to perform: codepipeline:ListPipelines...** のような権限エラーが出たら、CodePipelineのコンソールが別のリージョンで開いているかもしれません。修正するには、コンソールの **Region** ドロップダウンからワークショップを設定したリージョンを選んでください。
{{% /notice %}}

![CodePipeline Landing](/images/codepipeline/codepipeline_landing.png)

<!--
Once you are on the detail page for the specific CodePipeline, you can see the status along with links to the change and build details.
-->
指定したCodePipelineの詳細ページでは、ステータスと変更とビルドの詳細へのリンクがあります。

![CodePipeline Details](/images/codepipeline/codepipeline_details.png)

<!--
{{% notice tip %}}
If you click on the "details" link in the build/deploy stage, you can see the output from the CodeBuild process.
{{% /notice %}}
-->
{{% notice tip %}}
ビルド/デプロイステージの"details"リンクをクリックすると、CodeBuildプロセスの出力を見ることができます。
{{% /notice %}}

<!--
To review the status of the deployment, you can run:
-->
デプロイメントのステータスを確認するには、次を実行します:

```
kubectl describe deployment hello-k8s
```

<!--
For the status of the service, run the following command:
-->
serviceのステータスは、次を実行して確認します:

```
kubectl describe service hello-k8s
```

<!--
#### Challenge:
**How can we view our exposed service?**
-->
#### チャレンジ:
**公開されているサービスはどうやって確認をするのでしょうか?**

<!--
**HINT:** Which kubectl command will get you the Elastic Load Balancer (ELB) endpoint for this app?
-->
**ヒント:** このアプリをElastic Load Balancer(ELB)エンドポイントは、どのkubectlコマンドで取得できますか?

<!--
 {{%expand "Expand here to see the solution" %}}

 Once the service is built and delivered, we can run the following command to get the Elastic Load Balancer (ELB) endpoint and open it in a browser.
 If the message is not updated immediately, give Kubernetes some time to deploy the change.

 ```
 kubectl get services hello-k8s -o wide
 ```

{{% notice info %}}
This service was configured with a [LoadBalancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/) so,
an [AWS Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/) is launched by Kubernetes for the service.
The EXTERNAL-IP column contains a value that ends with "elb.amazonaws.com" - the full value is the DNS address.
{{% /notice %}}
{{% /expand %}}
-->
 {{%expand "答えはこちら" %}}

 serviceがビルドされてデプロイされると、次のコマンドでElastic Load Balancer(ELB)のエンドポイントを取得し、ブラウザで開くことができます。
 メッセージがアップデートされていなかったら、変更がデプロイされるまでに、少し時間を置いてください。

 ```
 kubectl get services hello-k8s -o wide
 ```

{{% notice info %}}
このserviceは[LoadBalancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/)が設定されているので、service用の[AWS Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)がKubernetesによって起動されています。
"elb.amazonaws.com"で終わる文字列を含むEXTERNAL-IP列にあるものがDNSアドレスです。
{{% /notice %}}
{{% /expand %}}
