# Elasticsearch 7.x クラスタ検証
全ノードに cluster.initial_master_nodes の指定がされている構成での検証。
ノード es06 は cluster.initial_master_nodes: es06 を指定し、es00-05のクラスタに入らない

## /splitbrain
7.x系で3ノードでクラスタ構築後、さらに3ノードを追加する。

まず、初期3ノードでクラスタを構築する。

```sh
cd splitbrain
make
make up
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
172.29.0.4 dimr * es02
172.29.0.2 dimr - es01
172.29.0.5 dimr - es00
```

次に、3ノード追加する。

```sh
make scale-out
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
172.29.0.4 dimr * es02
172.29.0.2 dimr - es01
172.29.0.8 dimr - es04
172.29.0.5 dimr - es00
172.29.0.6 dimr - es05
172.29.0.7 dimr - es03
```

3ノード停止し、3ノード構成に変更する。

```sh
docker-compose stop es00
docker-compose stop es01
docker-compose stop es02
```

```sh
curl http://localhost:9203/_cat/nodes\?h\=ip,node.role,master,name
172.29.0.7 dimr * es03
172.29.0.8 dimr - es04
172.29.0.6 dimr - es05
```

cluster.initial_master_nodesが設定されている1ノード起動する。

```sh
docker-compose -f docker-compose.6.yml up -d
```

ノードが既存クラスタに追加されていないことを確認する。

```sh
curl http://localhost:9203/_cat/nodes\?h\=ip,node.role,master,name
172.29.0.7 dimr * es03
172.29.0.8 dimr - es04
172.29.0.6 dimr - es05
```

ノードが単独のクラスタを構成していることを確認する。

```sh
curl http://localhost:9206/_cat/nodes\?h\=ip,node.role,master,name
172.29.0.2 dimr * es06
```

最後にコンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```
