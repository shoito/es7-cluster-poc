# Elasticsearch 7.x クラスタ検証

es00 が master ノードなる 2 ノード構成での検証。

以下が設定されている。

```
cluster.initial_master_nodes=es00
```

## /sb-2nodes

7.x 系で 2 ノードでクラスタ構築後、それぞれ停止して検証する。

まず、初期 2 ノードでクラスタを構築する。

```sh
cd sb-2nodes
make
make up
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
192.168.128.3 dimr * es00
192.168.128.4 dimr - es01
```

次に、es01(非 master ノード)を停止する。

```sh
docker-compose stop es01
```

しばらくしてからノードの状態を確認する。
マスタノードは停止していないため、API リクエストは正常にレスポンスを返す。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
192.168.128.3 dimr * es00
```

一旦コンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```

再度、初期 2 ノードでクラスタを構築する。

```sh
make
make up
```

しばらくしてからノードの状態を確認する。

```sh
$ curl http://localhost:9200/_cat/nodes\?h\=ip,node.role,master,name
192.168.128.3 dimr * es00
192.168.128.4 dimr - es01
```

次に、es00(master ノード)を停止する。

```sh
docker-compose stop es00
```

しばらくしてからノードの状態を確認する。
2 ノード構成だったため、マスタノードが停止後、残りの 1 ノードがマスタ昇格することはない。
そのため、API リクエストはエラーになる。

```sh
$ curl http://localhost:9201/_cat/nodes\?h\=ip,node.role,master,name
{"error":{"root_cause":[{"type":"master_not_discovered_exception","reason":null}],"type":"master_not_discovered_exception","reason":null},"status":503}
```

最後にコンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```
