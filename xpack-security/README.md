# Elasticsearch 7.x 認証機能の検証

```sh
cd xpack-security
make
make up
```

この後は、 `localhost:5601` や `localhost:9200` などにアクセスして認証が必要なことを確認できる。

最後にコンテナ、ネットワーク、ボリュームなどを削除する。

```sh
make clean
```
