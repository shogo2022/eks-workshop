---
title: "Ingress"
date: 2019-04-09T00:00:00-03:00
weight: 13
draft: false
---

#### Ingress

<!--
What is Ingress?
Ingress, added in Kubernetes v1.1, exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.
-->
Ingressとは何か?
IngressはKubernetes v1.1で追加され、クラスタ外部からのHTTPおよびHTTPSをクラスタ内のサービスにルーティングします。通信のルーティングはIngressリソースで定義されたルールで制御されます。

{{< output >}}
  internet
      |
  [ Ingress ]
  --|-----|--
  [ Services ]
{{< /output >}}

<!--
An Ingress can be configured to give services externally-reachable URLs, load balance traffic, terminate SSL, and offer name based virtual hosting. An Ingress controller is responsible for fulfilling the Ingress, usually with a load balancer, though it may also configure your edge router or additional frontends to help handle the traffic.
-->
Ingressはサービスに外部からアクセス可能なURLの付与や、通信のロードバランス、SSL終端、そして名前によるvirtual hostingも設定できます。Ingress controllerがIngressの機能を満たすように動いており、通常はロードバランサーが使われますが、通信を制御するためにエッジルータや他のフロントエンドを設定することもあります。

<!--
An Ingress does not expose arbitrary ports or protocols. Exposing services other than HTTP and HTTPS to the internet typically uses a service of type NodePort or LoadBalancer.
-->
Ingressは他のポートやプロトコルの公開には使われません。HTTPとHTTPS以外のインターネットへの公開には通常、NodePortやLoadBalancerが使われます。

<!--
You must have an ingress controller to satisfy an Ingress. Only creating an Ingress resource has no effect.
-->
Ingressを使うにはingress controllerが必要です。Ingressリソースだけを作成しても効果はありません。

<!--
You may need to deploy an Ingress controller such as [AWS ALB Ingress Controller](https://github.com/kubernetes-sigs/aws-alb-ingress-controller). You can choose from a number of Ingress controllers.
-->
[AWS ALB Ingress Controller](https://github.com/kubernetes-sigs/aws-alb-ingress-controller)のようなingress controllerをデプロイする必要があるかもしれません。Ingress controllerには何種類かあります。

<!--
Ideally, all Ingress controllers should fit the reference specification. In reality, the various Ingress controllers operate slightly differently.
-->
理想ではすべてのIngress controllerがリファレンスの仕様通りであればいいのですが、現実では様々なIngress controllerは異なった動きを見せます。

<!--
#### The Ingress Resource
-->
#### Ingressリソース

<!--
A minimal ingress resource example for [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/):
-->
最小構成のingressリソースの例です [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/):

{{< output >}}
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /testpath
        backend:
          serviceName: test
          servicePort: 80
{{< /output >}}

<!--
As with all other Kubernetes resources, an Ingress needs `apiVersion`, `kind`, and `metadata` fields. The name of an Ingress object must be a valid [DNS subdomain name](https://kubernetes.io/docs/concepts/overview/working-with-objects/names#dns-subdomain-names). For general information about working with config files, see [deploying applications](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/), [configuring containers](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/), [managing resources](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/). Ingress frequently uses annotations to configure some options depending on the Ingress controller, an example of which is the [rewrite-target annotation](https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/rewrite/README.md). Different [Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers) support different annotations. Review the documentation for your choice of Ingress controller to learn which annotations are supported.
-->
他のKubernetesリソースと同じように、Ingressは `apiVersion` 、 `kind` 、そして `metadata` が必要です。Ingressオブジェクトの名前は、有効な[DNSサブドメイン名](https://kubernetes.io/docs/concepts/overview/working-with-objects/names#dns-subdomain-names)である必要があります。一般的な設定ファイルの情報については、[deploying applications](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)、 [configuring containers](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)、 [managing resources](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/)を参照してください。IngressはIngress controllerによりますが、いくつかのオプションを設定するのに[rewrite-target annotation](https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/rewrite/README.md)などのannotationをよく使います。[Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers)毎に異なるannotationをサポートしています。どのannotationがサポートされているかは、選んだIngress Controllerのドキュメントを読み学習してください。

<!--
The Ingress [spec](https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status) has all the information needed to configure a load balancer or proxy server. Most importantly, it contains a list of rules matched against all incoming requests. Ingress resource only supports rules for directing HTTP traffic.
-->
Ingressの[spec](https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status)はロードバランサーやプロキシサーバを設定するのに必要な情報が全て含まれています。最も重要なのは、受け取るリクエストの全てに合致するルールがリストされていることです。IngressリソースはHTTP通信のルールのみサポートしています。

<!--
#### Ingress rules
-->
#### Ingress　ルール

<!--
Each http rule contains the following information:
-->
それぞれのhttpルールは以下の情報を含みます:

<!--
- An optional host. In this example, no host is specified, so the rule applies to all inbound HTTP traffic through the IP address specified. If a host is provided (for example, foo.bar.com), the rules apply to that host.
- A list of paths (for example, `/testpath`), each of which has an associated backend defined with a `serviceName` and `servicePort`. Both the host and path must match the content of an incoming request before the load balancer will direct traffic to the referenced service.
- A backend is a combination of service and port names as described in the [Services doc](https://kubernetes.io/docs/concepts/services-networking/service/). HTTP (and HTTPS) requests to the Ingress matching the host and path of the rule will be sent to the listed backend.
-->
- host(オプション)。この例ではhostは指定されていないので、IPアドレスに来たHTTPリクエスト全てに合致します。host(例 foo.bar.com)があった場合は、ルールはそのhostに適用されます。
- パス(例 `/testpath`)のリスト。パスはそれぞれ `serviceName` と `servicePort` で定義されたバックエンドに紐づけられている。ロードバランサーがサービスに通信を流すためには、hostとpathの両方がHTTPリクエストと合致している必要がある。
- バックエンドは[Services doc](https://kubernetes.io/docs/concepts/services-networking/service/)にある通り、Serviceとポート名の組み合わせ。IngressへのHTTP(とHTTPS)リクエストでhostとpathがルールに合致するものはバックエンドに送られる。

<!--
A default backend is often configured in an Ingress controller that will service any requests that do not match a path in the spec.
-->
specのどのパスにも合致しないリクエストを処理するデフォルトのバックエンドは通常Ingress Controllerで設定されます。

<!--
#### Default Backend
-->
#### デフォルトのバックエンド

<!--
An Ingress with no rules sends all traffic to a single default backend. The default backend is typically a configuration option of the [Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers) and is not specified in your Ingress resources.
-->
ルールがないIngressは単一のバックエンドに通信を流します。デフォルトのバックエンドは通常[Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers)の設定オプションでIngressリソースでは指定されません。

<!--
If none of the hosts or paths match the HTTP request in the Ingress objects, the traffic is routed to your default backend.
-->
IngressオブジェクトにHTTPリクエストに合致するhostやパスがない場合、通信はデフォルトのバックエンドに送られます。

<!--
{{% notice info %}}
[Click here](https://kubernetes.io/docs/concepts/services-networking/ingress/) to read more on that topic.
{{% /notice %}}
-->
{{% notice info %}}
詳しい情報は[こちら](https://kubernetes.io/docs/concepts/services-networking/ingress/)
{{% /notice %}}
