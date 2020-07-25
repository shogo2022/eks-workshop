---
title: "Custom Resource Definition"
chapter: true
weight: 270
tags:
  - advanced
  - operations
  - crd
---

# Custom Resource Definition
<!--
### Introduction
-->
### 紹介

<!--
In this Chapter, we will review the Custom Resource Definition (CRD) concept, and some examples of usage.
-->
この章では、Custom Resource Definition (CRD)の概要と使用例を見ていきます。

<!--
In Kubernetes API, a resource is an endpoint storing the API objects in a collection.
As an example, the pods resource contains a collection of Pod objects.
-->
Kubernetes APIでは、リソースはAPIオブジェクトの集まりを保存するエンドポイントです。
例えば、podリソースはPodオブジェクトの集まりです。

<!--
CRD’s are extensions of Kubernetes API that stores collection of API objects of certain kind. They extend the Kubernetes API or allow you to add your own API into the cluster.
-->
CRDは任意の種類の APIオブジェクトの集まりを保存するKubernetes APIを拡張したものです。Kubernetes APIを拡張したり、自身の APIを追加したりできます。

<!--
To create a CRD, you need to create a file, that defines your object kinds and lets the API Server manage the lifecycle. Applying a CRD into the cluster makes the Kubernetes API server to serve the specified custom resource.
-->
CRDを作るには、オブジェクトの種類を定義したファイルを作成し、APIサーバに管理をさせるようにします。クラスタにCRDを適用する事で、Kubernetes APIサーバが任意のカスタムリソースを提供できるようになります。

<!--
When a CRD is created, the Kubernetes API creates a new RESTful resource path, that can be accesed by a cluster or a single namespace.
-->
CRDが作成されると、Kubernetes APIは新しくRESTfulなリソースパスを作成し、クラスタもしくは名前空間からアクセスできるようになります。
