# Elasticsearch 7.x クラスタ検証
es00-02の3ノードのみ cluster.initial_master_nodes の指定がされている構成での検証。

以下が設定されている。

```
cluster.initial_master_nodes=es00,es01,es02
```

## /scaleout-no-imn
7.x系で3ノードでクラスタ構築後、さらに3ノードを追加する。

まず、初期3ノードでクラスタを構築する。

```sh
cd scaleout-no-imn
make
make up
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
172.24.0.3 dimr * es00
172.24.0.2 dimr - es02
172.24.0.5 dimr - es01
```

次に、3ノード追加する。
※cluster.initial_master_nodesが指定されていない

```sh
make scale-out
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
172.24.0.7 dimr - es03
172.24.0.3 dimr * es00
172.24.0.2 dimr - es02
172.24.0.5 dimr - es01
172.24.0.8 dimr - es05
172.24.0.6 dimr - es04
```

3ノード停止し、3ノード構成に変更する。
※cluster.initial_master_nodesが設定された3ノードを停止

```sh
docker-compose stop es00
docker-compose stop es01
docker-compose stop es02
```

```sh
curl http://localhost:9203/_cat/nodes\?h\=ip,node.role,master,name
172.24.0.8 dimr - es05
172.24.0.7 dimr * es03
172.24.0.6 dimr - es04
```

1ノード起動する。

```sh
docker-compose -f docker-compose.6.yml up -d
```

ノードがクラスタに追加されていることを確認する。

```sh
curl http://localhost:9203/_cat/nodes\?h\=ip,node.role,master,name
172.24.0.2 dimr - es06
172.24.0.8 dimr - es05
172.24.0.7 dimr * es03
172.24.0.6 dimr - es04
```

最後にコンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```