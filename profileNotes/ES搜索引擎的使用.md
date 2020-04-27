## ES搜索引擎的使用

### 1.实例化及连接

1. 安装elasticsearch模块'
   pip install elasticsearch

   pip install -i https://pypi.doubanio.com/simple/             elasticsearch

```python
from elasticsearch import Elasticsearch
# 远程连接ES
es = Elasticsearch(
    ["192.168.1.10", "192.168.1.11", "192.168.1.12"], # 连接集群，以列表的形式存放各节点的IP地址
    sniff_on_start=True,    # 连接前测试
    sniff_on_connection_fail=True,  # 节点无响应时刷新节点
    sniff_timeout=60    # 设置超时时间
)
es = Elasticsearch(
        ['10.10.40.15'],
        # http_auth=('elastic', 'passwd'),
        port=9200
    )
# 配置忽略响应状态码'
es = Elasticsearch(['127.0.0.1:9200'],ignore=400)  # 忽略返回的400状态码
es = Elasticsearch(['127.0.0.1:9200'],ignore=[400, 405, 502])  # 以列表的形式忽略多个状态码
# 连接本地ES
 es = Elasticsearch()    # 默认连接本地elasticsearch
 es = Elasticsearch(['127.0.0.1:9200'])  # 连接本地9200端口
```

###    2.架构及原理（https://www.cnblogs.com/tgzhu/p/6098339.html）

**核心概念**

------

- **集群（Cluster):** 包含一个或多个具有相同 cluster.name 的节点.

1. 1. 集群内节点协同工作，共享数据，并共同分担工作负荷。
   2. 由于节点是从属集群的，集群会自我重组来均匀地分发数据. 
   3. cluster Name是很重要的，因为每个节点只能是群集的一部分，当该节点被设置为相同的名称时，就会自动加入群集。
   4. 集群中通过选举产生一个mater节点，它将负责管理集群范畴的变更，例如创建或删除索引，添加节点到集群或从集群删除节点。master 节点无需参与文档层面的变更和搜索，这意味着仅有一个 master 节点并不会因流量增长而成为瓶颈。任意一个节点都可以成为 master 节点。我们例举的集群只有一个节点，因此它会扮演 master 节点的角色。
   5. 作为用户，我们可以访问包括 master 节点在内的集群中的任一节点。每个节点都知道各个文档的位置，并能够将我们的请求直接转发到拥有我们想要的数据的节点。无论我们访问的是哪个节点，它都会控制从拥有数据的节点收集响应的过程，并返回给客户端最终的结果。这一切都是由 Elasticsearch 透明管理的

- **节点(node):** 一个节点是一个逻辑上独立的服务，可以存储数据，并参与集群的索引和搜索功能, 一个节点也有唯一的名字，群集通过节点名称进行管理和通信.

- **索引（Index)：** 索引与关系型数据库实例(Database)相当。索引只是一个 逻辑命名空间，它指向一个或多个分片(shards)，内部用Apache Lucene实现索引中数据的读写

- **文档类型（Type）：**相当于数据库中的table概念。每个文档在ElasticSearch中都必须设定它的类型。文档类型使得同一个索引中在存储结构不同文档时，只需要依据文档类型就可以找到对应的参数映射(Mapping)信息，方便文档的存取

- **文档（Document) ：**相当于数据库中的row， 是可以被索引的基本单位。例如，你可以有一个的客户文档，有一个产品文档，还有一个订单的文档。文档是以JSON格式存储的。在一个索引中，您可以存储多个的文档。请注意，虽然在一个索引中有多分文档，但这些文档的结构是一致的，并在第一次存储的时候指定, 文档属于一种 *类型(type)*，各种各样的类型存在于一个 *索引* 中。你也可以通过类比传统的关系数据库得到一些大致的相似之处：

  ```
  关系数据库       ⇒ 数据库 ⇒ 表    ⇒ 行    ⇒ 列(Columns)
  Elasticsearch  ⇒ 索引   ⇒ 类型  ⇒ 文档   ⇒ 字段(Fields)
  ```

- 模拟示意图如：

- ![img](https://images2015.cnblogs.com/blog/1004194/201611/1004194-20161124180138409-1933404824.png)

- **Mapping：** 相当于数据库中的schema，用来约束字段的类型，不过 Elasticsearch 的 mapping 可以自动根据数据创建

- **分片(shard) ：**是 工作单元(worker unit) 底层的一员，用来分配集群中的数据，它只负责保存索引中所有数据的一小片。

1. 1. 分片是一个独立的Lucene实例，并且它自身也是一个完整的搜索引擎。
   2. 文档存储并且被索引在分片中，但是我们的程序并不会直接与它们通信。取而代之，它们直接与索引进行通信的
   3. 把分片想象成一个数据的容器。数据被存储在分片中，然后分片又被分配在集群的节点上。当你的集群扩展或者缩小时，elasticsearch 会自动的在节点之间迁移分配分片，以便集群保持均衡
   4. 分片分为 主分片(primary shard) 以及 从分片(replica shard) 两种。在你的索引中，每一个文档都属于一个主分片
   5. 从分片只是主分片的一个副本，它用于提供数据的冗余副本，在硬件故障时提供数据保护，同时服务于搜索和检索这种只读请求
   6. 索引中的主分片的数量在索引创建后就固定下来了，但是从分片的数量可以随时改变。
   7. 一个索引默认设置了5个主分片，每个主分片有一个从分片对应

### 3.基础操作（增删改查）

https://blog.csdn.net/douhh_sisy/article/details/102610641

本次项目中的精准查询语句

```
{'query': {"bool": {"must": [{"match_phrase": {'字段名': 属性值}}]}}}
```

### 4.查询操作（query）

https://www.cnblogs.com/zxyza/p/9772009.html

https://www.cnblogs.com/maoruqiang/p/11509873.html#_lab23_4

简易查询

##### 1.排序sort、分页、布尔查询bool

```python
#排序 sort  不是所有的字段都能排序，比如名字
    GET s18/doc/_search
        {
            "query":{
                "match_all":{}
            },
            "sort": [  #排序
                {
                    "age":{
                        "order": "asc" #升序，desc降序
                    }
                }
            ]
        }
        
#分页 内部会先排好序，保证翻页后不会出现已经返回过的数据
    GET s18/doc/_search
    {
        "query":{
               "match_all":{}
           },
        "from": 0,  #从0条开始
        "size": 2  #返回2条数据
    }

#布尔查询bool: should(or)  must(and)  must_not(not)，must_not
    GET s18/doc/_search
    {
        "query": {
            "bool": {
                "should": [  #名字是sybil或者年龄18岁
                    {
                        "match": {
                            "name": "sybil"
                        }
                    },
                    {
                        "match": {
                            "age": "18"
                        }
                    }
                ]
            }
        }
    }
    
    '查询性别是男的，年龄18'
    GET s18/doc/_search
    {
        "query": {
            "bool":{
                "must": [
                    {
                        "match": {
                            "age": "18"
                        }
                    },
                    {
                         "match": {
                        "sex": "男"
                        }
                    }
                ]
            }
        }
    }
    
#查询年龄大于19岁的男的  filter尽量用must配合，避免脏数据
    GET s18/doc/_search
    {
        "query": {
            "bool": {
                "must": [
                    {"match": {
                        "sex": "男"
                    }}
                ],
                "filter": {
                    "range": {
                        "age": {
                            "gte": 19,
                            "lte": 20
                        }
                    }
                }
            }
        }
    }
    
```

##### 2.高亮查询highlight

```python
#查询name是sybil的文档
#高亮查询，查询name是sybil的文档，查询的结果需要在前端才能体现高亮，因为是标签的效果
GET s18/doc/_search
{
  "query": {
    "match": {
      "name": "sybil"
    }
  },
  "highlight": {
    "fields": {
      "name": {}  #查询的结果用<em>标签包裹
    }
  }
}

GET s18/doc/_search
{
  "query": {
    "match": {
      "name": "sybil"  #会将我们这里定义的字段高亮显示
    }
  },
  "highlight": {  #可以使用pre_tags与post_tags自定义标签
    "pre_tags": "<b style='color:red;font-size:20px'>", 
    "post_tags": "<\b>", 
    "fields": {
      "name": {}  #这里指定字段后空着即可
    }
  }
}
```

##### 3.结果过滤_source

```python
#过滤出查询结果中想要的字段
GET s18/doc/_search
{
  "query": {
    "match": {
      "name": "sybil"
    }
  },
  "_source": "name"  #单个字段
}

GET s18/doc/_search
{
  "query": {
    "match": {
      "name": "sybil"
    }
  },
  "_source": ["name", "age"]  #多个字段
}
```

##### 4.聚合查询

```python
#sum，查询所有男生的年龄
GET s18/doc/_search
{
  "query": {
    "match": {
      "sex": "男"
    }
  },
  "aggs": {
    "my_sum": {  #这是查询结果的键，可以自定义
      "sum": {  #这是聚合的方法
        "field": "age"  #指定聚合的依据
      }
    }
  }
}

#查询最大年龄的男生
GET s18/doc/_search
{
  "query": {
    "match": {
      "sex": "男"
    }
  },
  "aggs": {
    "my_max": {
      "max": {
        "field": "age"
      }
    }
  }
}

#查询最小年龄的男生
GET s18/doc/_search
{
  "query": {
    "match": {
      "sex": "男"
    }
  },
  "aggs": {
    "my_min": {
      "min": {
        "field": "age"
      }
    }
  }
}

#查询男生的平均年龄
GET s18/doc/_search
{
  "query": {
    "match": {
      "sex": "男"
    }
  },
  "aggs": {
    "my_avg": {
      "avg": {
        "field": "age"
      }
    }
  }
}

#分组，根据年龄，0-10，,0-20
GET s18/doc/_search
{
  "query": {
    "match": {
      "sex": "男"
    }
  },
  "aggs": {
    "my_group": {  #分组名称
      "range": {
        "field": "age",
        "ranges": [
          {  
            "from": 0,  #[0, 10)
            "to": 10
          },
          {
            "from": 10,
            "to": 20
          },
          {
            "from": 20,
            "to": 30
          }
        ]
      }
    }
  }
}

#分组，根据年龄，0-10，,0-20, 对每组年龄求和
GET s18/doc/_search
{
  "query": {
    "match": {
      "sex": "男"
    }
  },
  "aggs": {
    "my_group": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 0,
            "to": 10
          },
          {
            "from": 10,
            "to": 20
          },
          {
            "from": 20,
            "to": 30
          }
        ]
      },
      "aggs": {
        "my_sum": {
          "sum": {
            "field": "age"
          }
        }
      }
    }
  }
}
```

##### 5. ES的mapping映射

```python
#自定义索引的映射
PUT s2
{
  "mappings": {
    "doc":{  #类型
      "properties":{  #文档的属性
        "name":{
          "type":"text"
        },
        "age":{
          "type":"long"
        },
        "desc":{
          "type":"text"
        }
      }
    }
  }
}

#如果给该索引加文档时，额外增加了字段，那么mappings会自动增加该字段，使其能够成为查询条件
GET s2/_mapping
PUT s2/doc/2
{
  "name":"catmao",
  "age":30,
  "desc":"beautiful",
  "skill":"sleep"
}
GET s2/_mapping  #再次执行会发现字段多了skill

#这是由mappings的dynamic的三种状态，三种状态时，均可以缺省字段
dynamic为true时特征如上。

dynamic为false时，PUT添加的数据有额外字段时，Mapping不会自动添加，该字段也无法成为查询的条件。

dynamic为strict时，添加的数据不能有额外的字段，会直接报错
    PUT s6
    {
      "mappings": {
        "doc":{
          "dynamic":"strict",
          "properties":{
            "name":{
              "type":"text"
            }
          }
        }
      }
    }
#mapping的ignore_above，不会为超过该设定字符长度的字符串设定索引与存储，即无法成为有效的查询条件，仅对type为keyword的字段有效。
https://www.cnblogs.com/Neeo/articles/10789701.html
    PUT s7
    {
      "mappings": {
        "doc":{
          "properties":{
            "title":{
              "type":"keyword",
              "ignore_above":10
            }
          }
        }
      }
    }

    PUT s7/doc/2
    {
      "title":"从手机、平板电脑、路由器"
    }
    PUT s7/doc/1
    {
      "title":"1234567"
    }
    GET s7/doc/_search
    {
      "query": {
        "match": {
          "title": "1234567"
        }
      }
    }
    
#mappings参数之index,设置为false后，ES不会为该字段建立索引，其实是不会做分词
#mappings的index参数
    PUT s8
    {
      "mappings": {
        "doc":{
          "properties":{
            "t1":{
              "type":"text",
              "index":"true"
            },
            "t2":{
              "type":"text",
              "index":"false"
            }
          }
        }
      }
    }

    PUT s8/doc/1
    {
      "t1":"论母猪的厂前保养",
      "t2":"论母猪的厂后保养"
    }

    GET s8/doc/_search
    {
      "query": {
        "match": {
          "t2": "母猪"
        }
      }
    }
    
#mappings的copy_to，把一个字段的值复制给另一个，可以减少一次查询的次数
    PUT s9
    {
      "mappings": {
        "doc":{
          "properties":{
            "t1":{
              "type":"text",
              "copy_to":"full_name"  #复制给多个字段 ["f1", "f2"]
            },
            "t2":{
              "type":"text",
              "copy_to":"full_name"
            },
            "full_name":{
              "type":"text"
            }
          }
        }
      }
    }

    PUT s9/doc/1
    {
      "t1":"xxx",
      "t2":"ooo"
    }

    GET s9/doc/_search
    {
      "query": {
        "match": {
          "full_name": "xxx"
        }
      }
    }
```

##### 6.嵌套属性

```python
#嵌套类型
    PUT w1
    {
      "mappings": {
        "doc":{
          "properties":{
            "name":{
              "type":"text"
            },
            "age":{
              "type":"long"
            },
             "info":{  #info字段嵌套两个字段
                "properties":{
                  "addr":{
                    "type":"text"
                  },
                  "tel":{
                    "type":"long"
                  }
                }
        }
          }
        }
      }
    }

    PUT w1/doc/1  #插入一条数据
    {
      "name":"tom",
      "age":18,
      "info":{  
        "addr":"北京",
        "tel":"10010"
      }
    }

    GET w1/doc/_search
    {
      "query": {
        "match": {
          "info.tel": "10010"  #以嵌套字段的属性为查询条件时，直接点语法即可
        }
      }
    }
```

##### 7.settings设置主从分片

```python
#主分片一旦设置无法更改，复制分片可以
PUT w2
{
  "mappings": {
    "doc":{
      "properties":{
        "title":{
          "type":"text"
        }
      }
    }
  }, 
  "settings": {  #通过settings设置
    "number_of_shards": 3,  #主分片数量，默认为5
    "number_of_replicas": 3  #复制分片数量，默认为1
  }
}

GET w2
```

##### 8.match系列

![1576222421871](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1576222421871.png)

个人经验：es.search(index=" ")查询数据时，数据会自动被分页，且默认每页为十条数据，可以通过设置size属性值来改变每页数量，但size最大值为10000，若要查询一个索引中的所有数据，且不受分页限制，需要设置scorll属性，调用下列方法，即可拿到索引中的全部数据。返回一个列表

```python
def searchDatabase(index, body={"query": {"match_all": {}}}):
    queryData = es.search(index=index, scroll='5m', timeout='3s', body=body)
    mdata = queryData.get("hits").get("hits")
    if not mdata:
        return 'empty!'
    scroll_id = queryData["_scroll_id"]
    total = queryData["hits"]["total"]
    for i in range(total // 10 + 1):
        res = es.scroll(scroll_id=scroll_id, scroll='5m')  # scroll参数必须指定否则会报错
        mdata += res["hits"]["hits"]
    return mdata
```

返回值示例

```python
[
{'_index': '权限配置', '_type': '权限表', '_id': 'AW75C-IslATJfSw-kiqV', '_score': 1.0, '_source': {'Accessname': '权限名称', 'Sysname': '统一权限管理系统', 'Userid': '123', 'function': 'tyqx-1', 'operaion': 'tyqx-4', 'Flag': 1, 'crdt': '2019-12-12 15:38:51', 'Updt': '', 'Flag1': '', 'Flag2': '', 'Flag3': ''}}, 
{'_index': '权限配置', '_type': '权限表', '_id': 'AW796JP2lATJfSw-kiq_', '_score': 1.0, '_source': {'Accessname': '13日中午添加', 'Sysname': '自动化部署工具', 'Userid': '123', 'function': 'zdbs-3', 'operaion': 'zdbs-5', 'Flag': 1, 'crdt': '2019-12-13 14:18:24', 'Updt': '', 'Flag1': '', 'Flag2': '', 'Flag3': ''}}, 
]
```

