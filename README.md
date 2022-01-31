# cassandra-kube

cassandra-kube は、主にエッジコンピューティング環境において Kubernetes 上 で Cassandra を立ち上げ稼働させるための、概要説明と設定ファイルです。  
cassandra 構築設定については README もしくは deployment.yml を参照してください。

## 動作環境

* OS : Linux OS  
* CPU: ARM/AMD/Intel  
* Kubernetes  

## Cassandra について

Cassandra は 構造化データ、半構造化データ、および非構造化データを管理可能な分散型のオープンソースのNoSQLデータベースです。  
Cassandra は 速度やスケーラビリティ、堅牢性に優れているため、高速に移動する大量のデータを信頼性が高くスケーラブルな方法で処理することができます。  

## Cassandra の導入

`kubectl apply -f deployment.yml` コマンドを利用してください。

必要に応じて yml 内の設定を変更してください。

* Cassandra のデータの永続化先: `cassandra-data` の `hostPath`.`path` 部分
* Cassandra のバージョン: `image: cassandra:[バージョン]` の部分
* 使用するメモリ量: 環境変数定義 (`env`) のコメントアウトを解除し、`JVM_ARGS: "-Xms4G -Xmx4G"` のように記載

その他環境変数によるカスタマイズは、[こちら](https://hub.docker.com/_/cassandra)を参照してください。

また、特に問題がない場合、導入時点での最新版 Cassandra のご利用をおすすめします。  
[こちら](https://cassandra.apache.org/_/download.html)で最新バージョンを確認できます。  

## AION における Cassandra のデプロイ・稼働
本レポジトリの deployment.yml に AION において Cassandra をデプロイ・稼働させるために必要な ymlファイル が含まれています。

### ymlファイル（deployment.yml）の中身

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: cassandra
  name: cassandra
spec:
  replicas: 1
  selector:
    matchLabels:
      run: cassandra
  strategy:
    rollingUpdate:
  template:
    metadata:
      labels:
        run: cassandra
    spec:
      hostname: cassandra
      containers:
      - name: cassandra
        tty: true
        image: cassandra:4.0.1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 9042
        volumeMounts:
        - name: cassandra-data
          mountPath: /var/lib/cassandra
      volumes:
      - name: cassandra-data
        hostPath:
          path: /var/lib/aion/default/Data/cassandra
      # env:
      # - name: JVM_ARGS
      #   value: ""

```