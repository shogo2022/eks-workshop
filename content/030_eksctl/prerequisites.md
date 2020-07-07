---
title: "前提"
date: 2018-08-07T13:31:55-07:00
weight: 10
---

<!--
For this module, we need to download the [eksctl](https://eksctl.io/) binary:
-->
このモジュールでは、[eksctl](https://eksctl.io/)バイナリーをダウンロードする必要があります:

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```

<!--
Confirm the eksctl command works:
-->
eksctlコマンドが動くことを確認します:

```bash
eksctl version
```

<!--
Enable eksctl bash-completion
-->
eksctlのbash-completionを有効にします

```bash
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```
