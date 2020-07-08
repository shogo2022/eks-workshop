---
title: "Chartの作成"
date: 2018-08-07T08:30:11-07:00
weight: 10
---

<!--
Helm charts have a structure similar to:
-->
Helm chartは次のような構造になっています:

{{< output >}}
/eksdemo
  /Chart.yaml  # a description of the chart
  /values.yaml # defaults, may be overridden during install or upgrade
  /charts/ # May contain subcharts
  /templates/ # the template files themselves
  ...
{{< /output >}}

<!--
We'll follow this template, and create a new chart called **eksdemo** with the following commands:
-->
このテンプレートに沿って、以下のコマンドで**eksdemo**という新しいchartを作成します:

```sh
cd ~/environment
helm create eksdemo
```
