---
title: "Kubernetes Jobs"
date: 2018-11-18T00:00:00-05:00
weight: 20
draft: false
---

### Kubernetes Jobs

<!--
A `job` creates one or more pods and ensures that a specified number of them successfully terminate. As pods successfully complete, the job tracks the successful completions. When a specified number of successful completions is reached, the job itself is complete. Deleting a Job will cleanup the pods it created.
-->
`job` はpodが任意の回数だけ、問題なくterminateするまでひとつ以上のpodを作成します。jobはpodが問題なく完了したことをトラックしていて、指定回数だけ問題なくpodが完了すると、job自身が完了となります。Jobを削除すると作成されたpodも削除されます。

<!--
Let's start by creating the _job-whalesay.yaml_ manifest using this command
-->
次のコマンドで _job-whalesay.yaml_ マニフェストを作成しましょう

```bash
mkdir  ~/environment/batch_policy/

cat <<EoF > ~/environment/batch_policy/job-whalesay.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: whalesay
spec:
  template:
    spec:
      containers:
      - name: whalesay
        image: docker/whalesay
        command: ["cowsay",  "This is a Kubernetes Job!"]
      restartPolicy: Never
  backoffLimit: 4
EoF
```

<!--
Run a sample Kubernetes Job using the `whalesay` image.
-->
`whalesay` イメージを使って、サンプルJobを実行します。

```bash
kubectl apply -f job-whalesay.yaml
```

<!--
Wait until the job has completed successfully.
-->
jobが問題なく完了するまで待ちます。

```bash
kubectl get job/whalesay
```

{{< output >}}
NAME       DESIRED   SUCCESSFUL   AGE
whalesay   1         1            2m
{{< /output >}}

<!--
Confirm the output.
-->
出力を確認します。

```bash
kubectl logs -l job-name=whalesay
```

{{< output >}}
 ___________________________ 
< This is a Kubernetes Job! >
 --------------------------- 
    \
     \
      \     
                    ##        .            
              ## ## ##       ==            
           ## ## ## ##      ===            
       /""""""""""""""""___/ ===        
  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
       \______ o          __/            
        \    \        __/             
          \____\______/   
{{< /output >}}
