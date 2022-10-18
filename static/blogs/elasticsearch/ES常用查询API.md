# ES常用API

## 1. 查询ES集群信息
  - 请求接口：
      ``` shell
      crul -XGET http://localhost:9200
      ```
  - 响应结果：

      ``` json
      {
       "name": "localhost:9300",
       "cluster_name": "test",
       "cluster_uuid": "eWOew3jRR1aeNYW4hMy2Eg",
       "version": {
           "number": "5.4.1",
           "build_hash": "2cfe0df",
           "build_date": "2017-05-29T16:05:51.443Z",
           "build_snapshot": false,
           "lucene_version": "6.5.1"
       },
       "tagline": "You Know, for Search"
      }
      ```

## 2. 查询ES集群节点信息
   - 请求接口：
       ``` shell
       crul -XGET http://localhost:9200/_cat/allocation?v&s=node
       ```
   - 响应结果：
       ``` shell
       shards disk.indices disk.used disk.avail disk.total disk.percent host          ip            node
        2400      297.5gb    14.2tb     25.6tb     39.8tb           35 192.168.1.128 192.168.1.128 192.168.1.128:9301
        4513      967.1gb    14.2tb     25.6tb     39.8tb           35 192.168.1.128 192.168.1.128 192.168.1.128:9302
        4512      955.3gb    14.2tb     25.6tb     39.8tb           35 192.168.1.128 192.168.1.128 192.168.1.128:9303
        4512          1tb    18.9tb     20.8tb     39.8tb           47 192.168.1.129 192.168.1.129 192.168.1.129:9301
        2400        233gb    18.9tb     20.8tb     39.8tb           47 192.168.1.129 192.168.1.129 192.168.1.129:9302
        4514        1.3tb    18.9tb     20.8tb     39.8tb           47 192.168.1.129 192.168.1.129 192.168.1.129:9303
        2400      233.5gb    13.5tb     26.2tb     39.8tb           34 192.168.1.130 192.168.1.130 192.168.1.130:9301
        2401      300.5gb    13.5tb     26.2tb     39.8tb           34 192.168.1.130 192.168.1.130 192.168.1.130:9302
        4512      980.7gb    13.5tb     26.2tb     39.8tb           34 192.168.1.130 192.168.1.130 192.168.1.130:9303
        2400       79.4gb    11.2tb     28.6tb     39.8tb           28 192.168.1.131 192.168.1.131 192.168.1.131:9300
        4511        1.1tb    11.2tb     28.6tb     39.8tb           28 192.168.1.131 192.168.1.131 192.168.1.131:9301
        4511          1tb    11.2tb     28.6tb     39.8tb           28 192.168.1.131 192.168.1.131 192.168.1.131:9302
        4513        1.1tb    11.2tb     28.6tb     39.8tb           28 192.168.1.131 192.168.1.131 192.168.1.131:9303
        25                                                                                       UNASSIGNED
       ```
## 3. 创建索引
    
   - 请求接口:
        ``` shell
        curl -XPUT http://localhost:9200/[索引名]
        ```
   - 请求体：
        ``` json
        {
            "mappings": {
                "test[索引类型名]": {
                    "properties": {
                        "name": {
                                "type": "keyword"
                            },
                        "birthday": {
                                "type": "date",
                                "format": "strict_date_optional_time||epoch_millis||yyyy-MM-dd HH:mm:ss||yyyy-MM-dd"
                            },
                        "nickName": {
                                "type": "text"
                            }
                    }
                }
            }
        }
        ```
## 4. 添加索引数据
   - 请求接口：
       ``` shell
       curl -XPUT http://localhost:9200/[索引名]/[索引类型名]/[数据ID(可不提供)]
       ```
   - 请求体：
       ``` json
       {
            "name": "java",
            "birthday": "2019-09-05 16:07:59",
            "nickName": "123456"
       }
       ```
## 5. 根据数据ID查询索引数据
   - 请求接口：
       ``` shell
       curl -XGET http://localhost:9200/[索引名]/[索引类型名]/[数据ID]
       ```
   - 响应结果：
       ```json
       {
        "_index": "test",
        "_type": "test",
        "_id": "8604021004C84DEDA500DF1403325328",
        "_version": 1,
        "found": true,
        "_source": {
            "name": "java",
            "birthday": "2019-09-05 16:07:59",
            "nickName": "123456"
          }
        }
       ```
     
## 6. 根据数据ID更新索引数据

   - 请求接口：
        ```shell
        curl -XPOST http://localhost:9200/[索引名]/[索引类型名]/[数据ID]/_update?pretty
        ```
   - 请求体：
        ```json
        {
           "doc":{
                 "name": "java",
                 "birthday": "2019-09-05 16:07:59",
                 "nickName": "123456"
            }
        }
        ```
        
## 7. 查询索引下的所有数据
   - 请求接口：
       ```shell
       curl -XGET http://localhost:9200/[索引名]/_search
       ```
   - 响应结果：
       ```json
       {
            "took": 1,
            "timed_out": false,
            "_shards": {
                "total": 5,
                "successful": 5,
                "failed": 0
            },
            "hits": {
                "total": 1,
                "max_score": 1.0,
                "hits": [
                    {
                        "_index": "test",
                        "_type": "test",
                        "_id": "8604021004C84DEDA500DF1403325328",
                        "_score": 1.0,
                        "_source": {
                            "name": "java",
                            "birthday": "2019-09-05 16:07:59",
                            "nickName": "123456"
                        }
                    }
                ]
            }
        }
       ```
## 8. 查询索引结构
   - 请求接口：
       ```shell
       curl -XGET http://localhost:9200/[索引名]/_mapping?pretty
       ```
   - 响应结果：
       ```json
       {
            "test(索引名)": {
                "mappings": {
                    "test(索引类型名)": {
                        "properties": {
                            "name": {
                                "type": "keyword"
                            },
                            "birthday": {
                                "type": "date",
                                "format": "strict_date_optional_time||epoch_millis||yyyy-MM-dd HH:mm:ss||yyyy-MM-dd"
                            },
                            "nickName": {
                                "type": "text"
                            }
                        }
                    }
                }
            }
        }
       ```
## 9. 删除索引
   - 请求接口：
       ```shell
       curl -XDELETE http://localhost:9200/[索引名]
       ```
   - 响应结果：
       ```json
       {
        "acknowledged": true
       }
       ```
