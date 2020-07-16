---
title: "後片付け"
date: 2019-04-09T00:00:00-03:00
weight: 12
draft: false
---


<!--
#### Remove The Namespace
-->
#### 名前空間の削除

<!--
Let's delete the namespace for this exercise:
-->
このハンズオンの名前空間を削除します:

```bash
rm -f test-creds
rm -f podconsumingsecret.yaml
kubectl delete ns secretslab
```
Output: 
{{< output >}}
namespace "secretslab" deleted
{{< /output >}}

<!--
This cleans up the secret and pod we deployed for this lab.
-->
このハンズオンで使用したpodとsecretの後片付けは完了です。