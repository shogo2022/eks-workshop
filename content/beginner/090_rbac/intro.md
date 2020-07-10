---
title: "RBACとは?"
date: 2018-10-03T10:14:46-07:00
draft: false
weight: 10
---

<!--
According to [the official kubernetes docs:](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
-->
[公式のkubernetesドキュメント](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)にはこうあります:

> Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within an enterprise.
> (ロールベースのアクセス制御(RBAC)とはエンタープライズの中の個々のユーザの役割に応じてコンピュータやネットワークリソースへのアクセスを制御する方式のこと)

<!--
The core logical components of RBAC are:
-->
RBACの核となるコンポーネントは以下の通り:

<!--
**Entity**  
A group, user, or service account (an identity representing an application that
wants to execute certain operations (actions) and requires permissions to do so).
-->
**エンティティ**
グループ、ユーザ、サービスアカウント(あるオペレーション(アクション)を実行もしくはその操作を行う権限を与えられるアプリケーションを表すもの)

<!--
**Resource**  
A pod, service, or secret that the entity wants to access using the certain operations.
-->
**リソース**
エンティティがあるオペレーションを通じてアクセスを必要とするpod、service、もしくはsecret

<!--
**Role**  
Used to define rules for the actions the entity can take on various resources.
-->
**ロール**
エンティティが様々なリソースに対してとることができるアクションのルールを定義するのに使用される

<!--
**Role binding**  
This attaches (binds) a role to an entity, stating that the set of rules define the
actions permitted by the attached entity on the specified resources.
-->
**ロールバインディング**
エンティティにロールをアタッチ(紐づける)のに使用され、アタッチされたエンティティが特定のリソースに対して許可されるアクションを定義する

<!--
There are two types of Roles (Role, ClusterRole) and the respective bindings (RoleBinding, ClusterRoleBinding).
These differentiate between authorization in a namespace or cluster-wide.
-->
ロールには２種類(Role, ClusterRole)あり、それぞれに紐づけ(RoleBinding, ClusterRoleBinding)があります。
これらは認証が名前空間内のみか、クラスター全体かを表しています。

<!--
**Namespace**  
-->
**名前空間**

<!--
Namespaces are an excellent way of creating security boundaries, they also provide a unique scope for object names as the 'namespace' name implies. They are intended to be used in multi-tenant environments to create virtual kubernetes clusters on the same physical cluster.
-->
名前空間はセキュリティ境界として最適な方法です。また、'名前空間'の名前が表す通り、オブジェクトの名前にスコープを与えます。これは単一の物理クラスターの中に複数の仮想kubernetesクラスターを作るマルチテナント環境として使われることを想定しています。

<!--
### Objectives for this module
In this module, we're going to explore k8s RBAC by creating an IAM user called rbac-user who is authenticated to access the EKS cluster but is only authorized (via RBAC) to list, get, and watch pods and deployments in the 'rbac-test' namespace.
-->
### このモジュールの目的
このモジュールでは、k8sのRBACを学ぶためにrbac-userというIAMユーザを作成しEKSクラスターに接続できるようにし、(RBACにより)'rbac-test'という名前空間のpodおよびdeploymentのlist、get、そしてwatchのみがkっ良化されるようにします。

<!-- #TODO - need to understand this last sentence 
To achieve this, we'll create an IAM user, map that user to a kubernetes role, then perform kubernetes actions under that user's context.
-->