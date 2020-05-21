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
es00-02の3ノードのみ cluster.initial_master_nodes の指定がされている構成での検証。


## splitbrain
全ノードに cluster.initial_master_nodes の指定がされている構成での検証。
ノード es06 は cluster.initial_master_nodes: es06 を指定し、es00-05のクラスタに入らない。
