# Elasticsearch 7.x クラスタ検証

`Bootstrapping a cluster`
https://www.elastic.co/guide/en/elasticsearch/reference/master/modules-discovery-bootstrap-cluster.html

`cluster.initial_master_nodes` の検証を主に。
https://www.elastic.co/guide/en/elasticsearch/reference/current/discovery-settings.html#initial_master_nodes

> You should not use this setting when restarting a cluster or adding a new node to an existing cluster.

`should not` であり、べきじゃない、程度で、新たにノード追加する際に指定されていても、既存クラスタにジョインする。

## /scaleout

全ノードに cluster.initial_master_nodes の指定がされている構成での検証。

## /scaleout-no-imn

es00-02 の 3 ノードのみ cluster.initial_master_nodes の指定がされている構成での検証。

## splitbrain

全ノードに cluster.initial_master_nodes の指定がされている構成での検証。
ノード es06 は cluster.initial_master_nodes: es06 を指定し、es00-05 のクラスタに入らない。

## sp-2nodes

es00 が master ノードなる 2 ノード構成での検証。
es01(非 master ノード)が停止の場合はクラスタは正常に稼働を継続する。
es00(master ノード)が停止の場合は、es01 は master 昇格せず、クラスタは落ちた状態になる。

## sp-3nodes

3 ノード構成からノードを 1 ノードずつ停止していく検証。
1 ノードが停止の場合はクラスタは正常に稼働を継続する。
2 ノード目以降は sp-2nodes と同様の結果になる。
