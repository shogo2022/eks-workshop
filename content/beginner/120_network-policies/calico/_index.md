---
title: "Calicoを使ったネットワークポリシーの作成"
weight: 10
---

<!--
In this Chapter, we will create some network policies using [Calico](https://www.projectcalico.org/) and see the rules in action.
-->
このチャプターでは、[Calico](https://www.projectcalico.org/)を使ってネットワークポリシーを作り、実際に動くところをみていきます。

<!--
Network policies allow you to define rules that determine what type of traffic is allowed to flow between different services. Using network policies you can also define rules to restrict traffic. They are a means to improve your cluster's security.
-->
ネットワークポリシーは異なるサービス間でどのような通信が許可されるかのルールを定義します。これらはクラスタのセキュリティを高めるために使われます。

<!--
For example, you can only allow traffic from frontend to backend in your application.
-->
例として、アプリケーションのフロントエンドからバックエンドの通信のみを許可することが可能です。

<!--
Network policies also help in isolating traffic within namespaces. For instance, if you have separate namespaces for development and production, you can prevent traffic flow between them by restrict pod to pod communication within the same namespace.
-->
ネットワークポリシーは通信を名前空間内に止めるときも使えます。例えば、開発と商用の名前空間が異なる場合、pod間の通信を名前空間内に制限することで、名前空間を跨いだ通信を防ぐことができます。

![calico](/images/Project-Calico-logo-1000px.png)
