# Elasticsearch 7.x クラスタ検証

3 ノード構成からノードを 1 ノードずつ停止していく検証。

以下が設定されている。

```
cluster.initial_master_nodes=es00,es01,es02
```

## /3-nodes

7.x 系で 3 ノードでクラスタ構築後、それぞれ停止して検証する。

まず、初期 3 ノードでクラスタを構築する。

```sh
cd sb-3nodes
make
make up
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
192.168.128.2 dimr * es01
192.168.128.5 dimr - es00
192.168.128.3 dimr - es02
```

次に、es01 を停止する。

```sh
docker-compose stop es01
```

しばらくしてからノードの状態を確認する。
マスタノードが残り 2 ノードのうち 1 ノードに切り替わるため、API リクエストは正常にレスポンスを返す。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
192.168.128.5 dimr * es00
192.168.128.3 dimr - es02
```

2 ノード構成になってるので、sb-2nodes と同じ。

最後にコンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```
