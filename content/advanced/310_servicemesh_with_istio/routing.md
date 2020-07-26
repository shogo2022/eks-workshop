---
title: "トラフィック管理"
date: 2018-11-13T21:49:32+09:00
weight: 50
draft: false
---

<!--
## Create the default destination rules
-->
## デフォルトの宛先ルール(default destination rule)の作成

<!--
Deploying a microservice-based application in an Istio service mesh allows one to externally control service monitoring and tracing, request (version) routing, resiliency testing, security and policy enforcement, and more in a consistent manner across the services, and the application.
-->
Istioサービスメッシュでマイクロサービスアプリケーションをデプロイすることによって、外部からの監視と追跡やリクエストの(バージョン)ルーティング、障害テスト、セキュリティおよびポリシー強制などをサービス、アプリケーションに跨って制御することができます。

<!--
Before you can use Istio to control the Bookinfo version routing, you'll need to define the available versions, called [**subsets**](https://istio.io/docs/reference/config/networking/destination-rule/#Subset).
-->
Bookinfoのバージョンルーティングを使う前に、[**subsets**](https://istio.io/docs/reference/config/networking/destination-rule/#Subset)でバージョンを定義する必要があります。

<!--
In a continuous deployment scenario, for a given service, there can be distinct subsets of instances running different variants of the application binary. These variants are not necessarily different API versions. They could be iterative changes to the same service, deployed in different environments (prod, staging, dev, etc.). Common scenarios where this occurs include A/B testing, canary rollouts, etc. The choice of a particular version can be decided based on various criterion (headers, url, etc.) and/or by weights assigned to each version. Each service has a default version consisting of all its instances.
-->
サービスにおける継続的デプロイメントでは、異なるアプリケーションバイナリがそれぞれのインスタンスで稼働するサブセットが存在しています。これらはAPIバージョンが同じ可能性もあります。また、異なる環境(prod、staging、devなど)にデプロイされている同じサービスに対する変更かもしれません。 このシナリオはA/Bテストやカナリアロールアウトなどでよくみられます。バージョンは(header、urlなどの)様々な要因とそれぞれのバージョンの重み付けで選択されます。それぞれのサービスには全てのインスタンスを含むデフォルトのバージョンが存在します。

```bash
kubectl -n bookinfo apply \
  -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/destination-rule-all.yaml
```

<!--
We can display the destination rules with the following command.
-->
次のコマンドで宛先ルールを表示できます。

```bash
kubectl -n bookinfo get destinationrules -o yaml
```

<!--
## Route traffic to one version of a service
-->
## 任意のバージョンのサービスへトラフィックを誘導

<!--
To route to one version only, we apply virtual services that set the default version for the microservices. In this case, the virtual services will route all traffic to `reviews:v1` of the microservice.
-->
一つのバージョンのみに誘導するには、マイクロサービスのデフォルトバージョンをセットする仮想サービスを適用します。この例では、全てのトラフィックが `reviews:v1` に誘導されます。

```bash
kubectl -n bookinfo \
  apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-all-v1.yaml
```

<!--
We can display the virtual service with the following command.
-->
次のコマンドで仮想サービス(virtual service)を表示できます。

```bash
kubectl -n bookinfo get virtualservices bookinfo -o yaml
```

<!--
The subset is set to v1 for all reviews request.
-->
subsetが全てのreviewsリクエストをv1にするようにセットされています。

{{< output >}}
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
{{< /output >}}

<!--
Try now to reload the page multiple times, and note how only version 1 of reviews is displayed each time.
-->
ページを何回か更新してみて、全てバージョン1が表示されることを確認します。

<!--
## Route based on user identity
-->
## ユーザに基づいた経路制御

<!--
Next, we'll change the route configuration so that all traffic from a specific user is routed to a specific service version.
-->
次に、経路設定を変更して、あるユーザからの全てのトラフィックを任意のバージョンのサービスに誘導するようにします。

<!--
In this case, all traffic from a user named <span style="color:orange">*Jason*</span> will be routed to the service `reviews:v2`.
-->
この例では、 <span style="color:orange">*Jason*</span> というユーザからの全てのトラフィックが `reviews:v2` に誘導されるようにします。

```bash
kubectl -n bookinfo \
  apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
```

<!--
We can display the updated virtual service with the following command.
-->
アップデートされた仮想サービスを次のコマンドで確認します。

```bash
kubectl -n bookinfo get virtualservices reviews -o yaml
```

<!--
The subset is set to v1 in default and route v2 if the logged user is match with 'jason' for reviews request.
-->
subsetではreviewsのリクエストに対して、デフォルトがv1、ユーザが 'jason' の場合はv2に設定されています。

{{< output >}}
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
{{< /output >}}

<!--
To test:
-->
テスト:

<!--
- Click **Sign in** from the top right corner of the page.
- Log in using **jason** as user name with a blank password.
-->
- ページの右上にある **Sign in** をクリックします。
- ユーザ名 **jason** 、パスワードなしでログインします。

<!--
You will only see `reviews:v2` all the time. Others will see `reviews:v1`.
-->
常に `reviews:v2` が表示されますが、他の人には `reviews:1` が見えています。

<!--
## Injecting an HTTP delay fault
-->
## HTTPの遅延障害の試験

<!--
To test for resiliency, inject a 7s delay between the `reviews:v2` and ratings microservices for user **jason**. This test will uncover a bug that was intentionally introduced into the Bookinfo app.
-->
回復機能を試すために、 **jason** ユーザ用に `reviews:v2` とratingsマイクロサービスの間に7秒間の遅延を発生させます。これはBookinginfoアプリに故意に仕掛けられているバグをテストします。

```bash
kubectl -n bookinfo \
  apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-ratings-test-delay.yaml
```

<!--
We can display the updated virtual service with the following command.
-->
アップデートされた仮想サービスを表示するには次のコマンドを使います。

```bash
kubectl -n bookinfo get virtualservice ratings -o yaml
```

<!--
The subset is set to v1 in default and added 7s delay for all the request if the logged user is match with 'jason' for ratings.
-->
subsetではratingsに対してデフォルトではv1がセットされ、ログインしているユーザが 'jason' の場合には7秒の遅延が追加される様にセットされています。

{{< output >}}
spec:
  hosts:
  - ratings
  http:
  - fault:
      delay:
        fixedDelay: 7s
        percent: 100
    match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1
{{< /output >}}

<!--
Logout, then click **Sign in** from the top right corner of the page, using **jason** as the user name with a blank password. You will see the delays and it ends up display error for reviews. Others will see reviews without error.
-->
ログアウトして、右上の **Sign in** をクリックし、ユーザ名 **jason** とパスワードなしでログインします。reviewsにエラーが表示されているのが見えます。他のユーザは問題なくページが見えているはずです。

![istio timeout error](/images/istio/istio_bookinfo_timeout_error.png)

<!--
The timeout between the `productpage` and the reviews service is 6 seconds - coded as 3s + 1 retry for 6s total.
-->
`productpage` とreviewsサービス間のタイムアウトは、3秒+再試行1回の計6秒に設定されています。

<!--
To test for another resiliency, we will introduce an HTTP abort to the ratings microservices for the test user jason. The page will immediately display the “<span style="color:orange">*Ratings service is currently unavailable*</span>”
-->
他の回復機能を試すために、jason用にratingsマイクロサービスにHTTP abortを入れてみます。この場合はすぐにページに"<span style="color:orange">*Ratings service is currently unavailable*</span>"が表示されます

```bash
kubectl -n bookinfo \
  apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-ratings-test-abort.yaml
```

<!--
We can display the updated virtual service with the following command.
-->
アップデートされた仮想サービスは次のコマンドで表示できます。

```bash
kubectl -n bookinfo get virtualservice ratings -o yaml
```

<!--
The subset is set to v1 and by default returns an error message of "Ratings service is currently unavailable" below the reviewer name if the logged username matches 'jason'.
-->
subsetはデフォルトではv1にセットされていますが、ログインしているユーザが 'jason' の場合にはレビュワーの下に"Ratings service is currently unavailable"というエラーメッセージが表示される様になっています。

{{< output >}}
spec:
  hosts:
  - ratings
  http:
  - fault:
      abort:
        httpStatus: 500
        percent: 100
    match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: ratings
        subset: v1
  - route:
    - destination:
        host: ratings
        subset: v1
{{< /output >}}

<!--
To test, click **Sign in** from the top right corner of the page and login using **jason** for the user name with a blank password. As **jason** you will see the error message.
-->
テストするには、右上から **Sign in** をクリックして、ユーザ名 **jason** とパスワードなしでログインします。 **jason** でログインしているときはエラーメッセージが見えるはずです。

![istio timeout error 2](/images/istio/istio_bookinfo_timeout_error2.png)

<!--
Others (not logged in as **jason**) will see no error message.
-->
( **jason** でrログインしていない)他のユーザでは、エラーメッセージは表示されません。

![istio timeout no error](/images/istio/istio_bookinfo_timeout_no_error.png)

<!--
## Traffic Shifting
-->
## トラフィック移行

<!--
Next, we'll demonstrate how to gradually migrate traffic from one version of a microservice to another. In our example, we'll send <span style="color:orange">50% of traffic to reviews:v1</span> and <span style="color:blue">50% to reviews:v3</span>.
-->
次にあるバージョンのマイクロサービスに対するトラフィックを別バージョンへ段階的に移行する方法をお見せします。この例では、<span style="colorr:orange">reviews:v1に50％</span>、<span style="color:blue">reviews:v3に50％</span>のトラフィックを誘導します。

<!--
To get started, run this command to route all traffic to the v1 version of each microservice.
-->
始めるには、次のコマンドでそれぞれのマイクロサービスに対するトラフィックをv1に流す様に設定します。

```bash
kubectl -n bookinfo \
  apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-all-v1.yaml
```

<!--
Open the `Bookinfo `site in your browser. Notice that the reviews part of the page displays with no rating stars, no matter how many times you refresh.
-->
ブラウザで `Bookinfo` サイトを開きます。何回更新してもreviewsの部分に星が出てこないことを確認してください。

<!--
We can now transfer 50% of the traffic from `reviews:v1` to `reviews:v3`
-->
では、トラフィックの50％を `reviews:v1` から `reviews:v3` に移行します

```bash
kubectl -n bookinfo \
  apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
```

```bash
kubectl -n bookinfo get virtualservice reviews -o yaml
```

<!--
The subset is set to 50% of traffic to v1 and 50% of traffic to v3 for all reviews request.
-->
subsetはreviewsへのリクエストに対して、50%をv1に、50％をv3に流す様にセットされています。

{{< output >}}
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 50
    - destination:
        host: reviews
        subset: v3
      weight: 50
{{< /output >}}

<!--
To test it, refresh your browser over and over, and you'll see only reviews:v1 and reviews:v3.
-->
ブラウザを複数回更新してテストします。reviews:v1とreviews:v3のみが表示されます。

<!--
Assuming you decide tat the `reviews:v3` microservice is stable, you can route 100% of the traffic to it
-->
`reviews:v3` マイクロサービスが安定して稼働していると想定して、トラフィックの100％を向けてみます

```bash
kubectl -n bookinfo apply -f ${HOME}/environment/istio-${ISTIO_VERSION}/samples/bookinfo/networking/virtual-service-reviews-v3.yaml
```

<!--
Now when you refresh the `/productpage` you will always see `reviews:v3` (red colored star ratings).
-->
'/productpage' を更新すると `reviews:v3` (赤い星のレーティング)のみが常に表示されます。
