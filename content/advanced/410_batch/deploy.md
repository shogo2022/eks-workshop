---
title: "Argoのデプロイ"
date: 2018-11-18T00:00:00-05:00
weight: 40
draft: false
---

<!--
### Deploy Argo Controller
-->
### Argo Controllerのデプロイ

<!--
Argo run in its own namespace and deploys as a CustomResourceDefinition.
-->
Argoは自身の滑空間で実行され、CustomResourceDefinitionとして実装されます。

<!--
Deploy the Controller and UI.
-->
ControllerとUIをデプロイします。

```bash
kubectl create namespace argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/${ARGO_VERSION}/manifests/install.yaml
```

{{< output >}}
customresourcedefinition.apiextensions.k8s.io/clusterworkflowtemplates.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/cronworkflows.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/workflows.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/workflowtemplates.argoproj.io created
serviceaccount/argo created
serviceaccount/argo-server created
role.rbac.authorization.k8s.io/argo-role created
clusterrole.rbac.authorization.k8s.io/argo-aggregate-to-admin created
clusterrole.rbac.authorization.k8s.io/argo-aggregate-to-edit created
clusterrole.rbac.authorization.k8s.io/argo-aggregate-to-view created
clusterrole.rbac.authorization.k8s.io/argo-cluster-role created
clusterrole.rbac.authorization.k8s.io/argo-server-cluster-role created
rolebinding.rbac.authorization.k8s.io/argo-binding created
clusterrolebinding.rbac.authorization.k8s.io/argo-binding created
clusterrolebinding.rbac.authorization.k8s.io/argo-server-binding created
configmap/workflow-controller-configmap created
service/argo-server created
service/workflow-controller-metrics created
deployment.apps/argo-server created
deployment.apps/workflow-controller created
{{< /output >}}

<!--
### Configure the service account to run Workflows
-->
### Workflowsを実行するためのサービスアカウントの設定

<!--
In order for Argo to support features such as artifacts, outputs, access to secrets, etc. it needs to communicate with Kubernetes resources using the Kubernetes API. To communicate with the Kubernetes API, Argo uses a `ServiceAccount` to authenticate itself to the Kubernetes API. You can specify which `Role` (i.e. which permissions) the ServiceAccount that Argo uses by binding a `Role` to a `ServiceAccount` using a `RoleBinding`
-->
アーティファクトや出力、secretへのアクセスなどの機能をArgoがサポートするためには、Kubernetes APIを使ってKubernetes リソースとやりとりをする必要があります。Kubernetes APIとやりとりをするために、Argoは `ServiceAccount` を使って認証します。使う `Role` (例　権限)を指定し、 `RoleBinding` でArgoのServiceAccountと紐づけることができます。

```bash
kubectl -n argo create rolebinding default-admin --clusterrole=admin --serviceaccount=argo:default
```

<!--
{{% notice note %}}
Note that this will grant admin privileges to the default ServiceAccount in the namespace that the command is run from, so you will only be able to run Workflows in the namespace where the RoleBinding was made.
{{% /notice %}}
-->
{{% notice note %}}
これはコマンドが実行された名前空間でデフォルトのServiceAccountにアドミン権限を与えているので、このRoleBindingが作成された名前空間内のみでWorkflowを実施することができます。
{{% /notice %}}
