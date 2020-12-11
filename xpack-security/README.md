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

## 確認

```sh
# Basic 認証のユーザー名:パスワードがないため 401 エラー
$ curl localhost:9200 -v
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 9200 (#0)
> GET / HTTP/1.1
> Host: localhost:9200
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 401 Unauthorized
< WWW-Authenticate: Basic realm="security" charset="UTF-8"
< content-type: application/json; charset=UTF-8
< content-length: 381
<
* Connection #0 to host localhost left intact
{"error":{"root_cause":[{"type":"security_exception","reason":"missing authentication credentials for REST request [/]","header":{"WWW-Authenticate":"Basic realm=\"security\" charset=\"UTF-8\""}}],"type":"security_exception","reason":"missing authentication credentials for REST request [/]","header":{"WWW-Authenticate":"Basic realm=\"security\" charset=\"UTF-8\""}},"status":401}* Closing connection 0

# Basic 認証をする
$ curl localhost:9200 -v -u elastic:changeme
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 9200 (#0)
* Server auth using Basic with user 'elastic'
> GET / HTTP/1.1
> Host: localhost:9200
> Authorization: Basic ZWxhc3RpYzpjaGFuZ2VtZQ==
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=UTF-8
< content-length: 529
<
{
  "name" : "es00",
  "cluster_name" : "es-cluster",
  "cluster_uuid" : "HeLEixMxTPeiExSxaHqdWQ",
  "version" : {
    "number" : "7.9.3",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "c4138e51121ef06a6404866cddc601906fe5c868",
    "build_date" : "2020-10-16T10:36:16.141335Z",
    "build_snapshot" : false,
    "lucene_version" : "8.6.2",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
* Connection #0 to host localhost left intact
* Closing connection 0
```
