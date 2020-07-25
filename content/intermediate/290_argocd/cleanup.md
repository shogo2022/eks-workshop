---
title: "後片付け"
weight: 50
draft: false
---

<!--
Congratulations on completing the Continuous Deployment with ArgoCD module.
-->
ArgoCDを使っての継続的デリバリーのモジュールはこれで完了です。

<!--
This module is not used in subsequent steps, so you can remove the resources now, or at the end of the workshop:
-->
このモジュールは以降使われることはないので、リソースを削除します:
```
argocd app delete ecsdemo-nodejs
watch argocd app get ecsdemo-nodejs
```

<!--
Wait until all ressources are cleared with this message:
-->
次のメッセージが出て、全てのリソースが削除されるまで待ちます:
```
FATA[0000] rpc error: code = NotFound desc = applications.argoproj.io "ecsdemo-nodejs" not found 
```

<!--
And then delete ArgoCD from your cluster:
-->
ArgoCDをクラスタから削除します:
```
kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<!--
Delete namespaces created for this chapter:
-->
この章で作成した名前空間を削除します:
```
kubectl delete ns argocd
kubectl delete ns ecsdemo-nodejs
```

<!--
You may also delete the cloned repository `ecsdemo-nodejs` within your GitHub account.
-->
自身のGitHubアカウント内の `ecsdemo-nodejs` レポジトリも削除しておきましょう。