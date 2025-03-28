# モジュール3 のサンプル


### マネジメントコンソールや AWS CLIでクラスターを作成時に必要なIAMロール
* IAMロールに設定する許可ポリシー
  -  `AmazonEKSClusterPolicy`
* IAMロールに設定する信頼ポリシー: trust-eks.json

---

### マネジメントコンソールや AWS CLIでマネージドノードグループを作成時に必要なIAMロール
* IAMロールに設定する許可ポリシー: 
  - `AmazonEKSWorkerNodePolicy`
  - `AmazonEC2ContainerRegistryReadOnly`
  - `AmazonEKS_CNI_Policy`
* IAMロールに設定する信頼ポリシー: trust-ec2.json
---
### マネジメントコンソールや AWS CLIでFargateプロファイルを作成時に必要なIAMロール
* IAMロールに設定する許可ポリシー:
  - `AmazonEKSFargatePodExecutionRolePolicy`
* IAMロールに設定する信頼ポリシー: trust-eks-fargate-pod.json

---
### eksctlによるクラスター作成のサンプル 1

* VPCやサブネットも作成
* マネージドノードグループも作成
  - マネージドノードグループはPublicサブネットに作成 
* ラボ1で使用するものと同等

```
eksctl create cluster \
--name sample1-cluster \
--nodegroup-name sample1-nodes \
--node-type t3.small \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--managed \
--version 1.31 \
--region ap-northeast-1
```

---

### eksctlによるクラスター作成のサンプル 2

* VPCやサブネットも作成
* AZは指定
* マネージドノードグループも作成
  - マネージドノードグループはPublicサブネットに作成 

```
AWS_REGION=ap-northeast-1
eksctl create cluster \
  --name=sample2-cluster \
  --version 1.31 \
  --nodes=3 --managed \
  --region ${AWS_REGION} --zones ${AWS_REGION}a,${AWS_REGION}c
```
---

### eksctlによるクラスター作成のサンプル 3

* VPCやサブネットは事前に作成しておく
* サブネットIDを指定する
* マネージドノードグループも作成
  - マネージドノードグループはPrivateサブネットに作成 

```
eksctl create cluster \
--name sample3-cluster \
--vpc-public-subnets subnet-1111111,subnet-2222222  \
--vpc-private-subnets subnet-3333333,subnet-4444444 \
--nodegroup-name sample3-nodes \
--node-private-networking \
--node-type t3.small \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--managed \
--version 1.31 \
--region ap-northeast-1
```
---

### eksctlによるクラスター作成のサンプル 4

* VPCやサブネットは事前に作成しておく
* サブネットIDを指定する
* マネージドノードグループも作成
  - マネージドノードグループはPrivateサブネットに作成 
  - AMI のタイプを指定 

```
eksctl create cluster \
--name sample4-cluster \
--vpc-public-subnets subnet-1111111,subnet-2222222  \
--vpc-private-subnets subnet-3333333,subnet-4444444 \
--nodegroup-name sample4-nodes \
--node-private-networking \
--node-type t3.small \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--node-ami-family=Bottlerocket \
--managed \
--version 1.31 \
--region ap-northeast-1
```

---

### eksctlによるクラスター作成のサンプル 5

* Auto Mode を使用するクラスターの作成
* VPCやサブネットも作成

```
eksctl create cluster \
--name=sample5-cluster \
--enable-auto-mode     \
--version 1.31         \
--region ap-northeast-1
```

---

### eksctlによるクラスター作成のサンプル 6

* Fargateプロファイルを使用するクラスターの作成
* VPCやサブネットも作成
* この例では、FargateプロファイルのNamespaceとして次のものが適用される
  - `kube-system` と `default` 

```
eksctl create cluster \
--name sample6-cluster \
--version 1.31 \
--region ap-northeast-1 \
--fargate
```
---

### eksctlによるFargateプロファイルの作成のサンプル 

* 既存のクラスター(例ではmy-cluster)にFargateプロファイルを追加作成する
  - EKS クラスターの VPC に Private Subnet が必要
* この例では、FargateプロファイルのNamespaceに `prod` 、ラベルに `blue` を指定。

```
eksctl create fargateprofile \
    --cluster my-cluster \
    --name my-fargate-profile \
    --namespace prod \
    --labels stack=blue
```

---

### eksctlによるクラスター作成のサンプル (構成ファイルを使用)

* 構成ファイルを使用したクラスター作成
  - 下記のように `--dry-run` オプションを使用して構成ファイルのテンプレートを生成可能
    - `eksctl create cluster --name temp-cluster  --dry-run`
  - 構成ファイルの例
    - clusterconfig-public.yaml
      - マネージドノードグループをPublicサブネットに配置
    - clusterconfig-private.yaml
      - マネージドノードグループをPrivateサブネットに配置

```
eksctl create cluster -f  clusterconfig-public.yaml
```

---

### Mod3 demo 用: .kube/config　ファイルをみせる

```
cd
```

```
ls   -la
```

(.kubeがみえる）

```
cd .kube
```

```
ls
```

(configファイルがみえる）

```
cat config
```

このconfigファイルには、接続先のクラスターの情報や証明書などの情報が格納されています。
これらの情報は、kubectl で クラスターに接続するために必要になりますが、eksctl でEKSクラスターを作成したクライアントでは、このファイルは自動で作成されますので、EKSクラスター作成後、すぐにそのクライアントでクラスターを操作できます。


---

### Mod3 demo 用: eksctl を使用したクラスターの作成


準備:既存の config のバックアップ取得

ターミナルで mod3 のフォルダに移動しておく

```
eksctl create cluster -f  my-cluster.yaml
```

```
kubectl get nodes
```

```
kubectl get pods
```

```
kubectl get pods -A
```

```
cd
```

```
ls   -la
```

(.kubeがみえる）

```
cd .kube
```

```
ls
```

(configファイルがみえる）

後始末：my-clusterは削除する

```
eksctl delete cluster -f  my-cluster.yaml
```

バックアップの config ファイルを戻す

---

### Mod3 demo 用: Fargate プロファイルの作成と Fargate を使用する Pod の作成


準備:
```
kubectl create ns prod
```


```
eksctl create fargateprofile \
    --cluster example-cluster \
    --name my-fargate-profile \
    --namespace prod \
    --labels stack=blue
```

blue-pod.yaml をみせる

```
kubectl apply -f blue-pod.yaml
```

red-pod.yaml をみせる

```
kubectl apply -f red-pod.yaml
```

マネージメントコンソールで、example-clusterのコンピューティングタブで Fargate プロファイルをみせる

リソースタブで blue-pod をクリック

リソースタブで red-pod をクリック


後始末:

```
kubectl delete -f blue-pod.yaml
```

```
kubectl delete -f red-pod.yaml
```


```
eksctl delete fargateprofile \
    --cluster example-cluster \
    --name my-fargate-profile 
```

---

### Mod3 demo 用: マネコンで EKS の Kubernetes バージョンと eks プラットフォームバージョンをみせる

* ★マネコンで、example-cluster の「クラスター情報」でKubernetes のバージョンみせる
* ★「概要」タブの「詳細」の 右側にある 「プラットフォームバージョン」をみせる eks.24とか
* ★この eks.24 というバージョンから、対応するKubernetesの詳細なバージョンがわかる
* ★ドキュメント：
    - https://docs.aws.amazon.com/eks/latest/userguide/platform-versions.html#platform-versions-1-31
* ★このように、Kubernetes のバージョンと、Amazon EKS のプラットフォームバージョンの対応について、留意しておきましょう

---

### Mod3 demo 用: マネコンで EKS の クラスターのアップグレードとノードグループのアップグレードをみせる

* クラスターのアップグレード
    - ★マネコンで クラスター一覧をみせる
    - ★demo-cluster の [今すぐアップグレード]をクリック
    - ★Kubernetes のバージョンのリストをみせる。1.31しか選べないことを説明

* ノードグループのアップグレード
  - ★マネコンで demo-clutster を開く
  - ★[コンピューティング]タブをクリック
  - ★[ノードグループ]のセクションで、[今すぐ更新]をクリック
  - ★[更新戦略]のリストを開く
  - ★[ローリング更新]は、Pod のディスラプションバジェットという最低限維持すべきPod数の設定を考慮しながら更新する。
  - ★この場合、ディスラプションバジェットの設定を維持できない場合は更新ができない可能性があるが、アプリケーションの可用性は維持できる
  - ★[強制更新]は、Pod のディスラプションバジェットの設定は考慮せずに更新する。この場合は更新は強制的に行われるが、アプリケーションの実行に影響が出る可能性がある

