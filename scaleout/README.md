# Elasticsearch 7.x クラスタ検証

全ノードに cluster.initial_master_nodes の指定がされている構成での検証。

共通で以下が設定されている。

```
cluster.initial_master_nodes=es00,es01,es02
```

## /scaleout

7.x 系で 3 ノードでクラスタ構築後、さらに 3 ノードを追加する。

まず、初期 3 ノードでクラスタを構築する。

```sh
cd scaleout
make
make up
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
172.23.0.4 dimr * es01
172.23.0.3 dimr - es00
172.23.0.2 dimr - es02
```

次に、3 ノード追加する。

```sh
make scale-out
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
172.23.0.2 dimr - es02
172.23.0.4 dimr * es01
172.23.0.8 dimr - es05
172.23.0.3 dimr - es00
172.23.0.7 dimr - es03
172.23.0.6 dimr - es04
```

3 ノード停止し、3 ノード構成に変更する。

```sh
docker-compose stop es00
docker-compose stop es01
docker-compose stop es02
```

```sh
curl http://localhost:9203/_cat/nodes\?h\=ip,node.role,master,name
172.23.0.7 dimr - es03
172.23.0.6 dimr * es04
172.23.0.8 dimr - es05
```

cluster.initial_master_nodes が設定されている 1 ノード起動する。

```sh
docker-compose -f docker-compose.6.yml up -d
```

ノードがクラスタに追加されていることを確認する。

```sh
curl http://localhost:9203/_cat/nodes\?h\=ip,node.role,master,name
172.23.0.7 dimr - es03
172.23.0.6 dimr * es04
172.23.0.2 dimr - es06
172.23.0.8 dimr - es05
```

最後にコンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```
