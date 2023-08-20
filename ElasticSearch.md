# 一、ElasticSearch
## 1.介绍
Elasticsearch（简称为ES）是一个开源的分布式搜索和分析引擎，构建在Apache Lucene之上。它专注于实时数据分析和搜索，并且具有高性能、可扩展性和灵活性。Elasticsearch 主要用于处理大规模的数据集，从中提取有价值的信息，以便用于搜索、分析和可视化。

以下是 Elasticsearch 的一些重要特点和概念：

1. **分布式架构：** Elasticsearch 是一个分布式系统，数据被分割成多个分片（shards）并分布在不同的节点上。这使得 Elasticsearch 能够处理大规模数据，提供高吞吐量和低延迟的查询。

2. **实时性能：** Elasticsearch 支持实时索引和搜索，使得数据的变更能够在短时间内反映在搜索结果中，适用于实时监控、日志分析等场景。

3. **多种数据类型：** Elasticsearch 可以存储和处理各种不同类型的数据，包括文本、数字、地理位置、日期等。

4. **全文搜索：** Elasticsearch 基于 Lucene 引擎，具有强大的全文搜索和分析能力，支持多字段、模糊搜索、词条匹配等。

5. **多样化查询：** 支持丰富的查询语言，如基于 JSON 的查询、过滤器、聚合、模糊查询、范围查询等。

6. **分析和聚合：** Elasticsearch 提供了强大的聚合和分析功能，可以对数据进行统计、分组、求和、平均等操作。

7. **地理信息：** 支持地理坐标的索引和查询，适用于地理信息系统（GIS）和位置数据分析。

8. **插件生态系统：** Elasticsearch 提供了丰富的插件，可以扩展其功能，如可视化工具、安全性增强、数据导入等。

9. **开源和社区支持：** Elasticsearch 是开源项目，拥有强大的社区支持，可以获得丰富的文档、示例和问题解答。

Elasticsearch 在多个领域中得到广泛应用，例如：

- **日志分析：** 处理和分析大量的日志数据，用于故障排查、监控和可视化。

- **全文搜索引擎：** 用于构建搜索引擎、文档检索系统和电子商务网站的搜索功能。

- **数据分析：** 通过聚合和分析大量数据，从中获取洞察和统计信息。

- **地理信息系统：** 处理地理坐标数据，支持地理位置查询和分析。

总之，Elasticsearch 是一个功能强大的搜索和分析引擎，适用于多种场景，从实时数据分析到全文搜索。

## 2.安装
略

## 3. 一些概念
### 3.1 倒排索引
略

## 4.入门
### 4.1 HTTP
#### 4.1.1 索引
##### 4.1.1.1 创建
对比关系型数据库，创建索引等同于创建数据库。
![image](.img/es_索引创建.png)

##### 4.2.1.2 查看所有索引
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/_cat/indices?v

这里请求路径中的_cat表示查看的意思，indices表示索引，v表示将信息详细展现出来，所以整体含义就是查看当前 ES服务器中的所有索引，就好像 MySQL 中的 show tables 的感觉，服务器响应结果如下：
![image](.img/es_查看所有索引.png)

表头 | 含义
---|---
health | 当前服务器健康状态：green(集群完整) yellow(单点正常、集群不完整) red(单点不正常)
status | 索引打开、关闭状态
index | 索引名
uuid | 索引统一编号
pri | 主分片数量
rep | 副本数量
docs.count | 可用文档数量
docs.deleted | 文档删除状态（逻辑删除）
store.size | 主分片和副分片整体占空间大小
pri.store.size | 主分片占空间大小

##### 4.2.1.3 查看单个索引
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/shopping
![image](.img/es_查看单个索引.png)

##### 4.2.1.4 删除索引
向 ES 服务器发 DELETE 请求 ：http://127.0.0.1:9200/shopping

#### 4.1.2 文档
索引已经创建好了，接下来我们来创建文档，并添加数据。这里的文档可以类比为关系型数据库中的表数据，添加的数据格式为 JSON 格式。
##### 4.1.2.1 创建文档
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_doc
请求体内容为：
```
{
 "title":"小米手机",
 "category":"小米",
 "images":"http://localhost/xm.jpg",
 "price":3999.00
}
```
服务器响应结果如下：
```
{
 "_index"【索引】: "shopping",
 "_type"【类型-文档】: "_doc",
 "_id"【唯一标识】: "Xhsa2ncBlvF_7lxyCE9G", #可以类比为 MySQL 中的主键，随机生成
 "_version"【版本】: 1,
 "result"【结果】: "created", #这里的 create 表示创建成功
 "_shards"【分片】: {
 "total"【分片 - 总数】: 2,
 "successful"【分片 - 成功】: 1,
 "failed"【分片 - 失败】: 0
 },
 "_seq_no": 0,
 "_primary_term": 1
}
```
上面的数据创建后，由于没有指定数据唯一性标识（ID），默认情况下，ES服务器会随机生成一个。
如果想要自定义唯一性标识，需要在创建时指定：http://127.0.0.1:9200/shopping/_doc/1
==此处需要注意：如果增加数据时明确数据主键，那么请求方式也可以为 PUT==

##### 4.1.2.2 查看文档
查看文档时，需要指明文档的唯一性标识，类似于 MySQL 中数据的主键查询。
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/shopping/_doc/1

```
{
 "_index"【索引】: "shopping",
 "_type"【文档类型】: "_doc",
 "_id": "1",
 "_version": 2,
 "_seq_no": 2,
 "_primary_term": 2,
 "found"【查询结果】: true, # true 表示查找到，false 表示未查找到
 "_source"【文档源信息】: {
 "title": "华为手机",
 "category": "华为",
 "images": "http://www.gulixueyuan.com/hw.jpg",
 "price": 4999.00
 }
}
```

##### 4.1.2.3 修改文档
和新增文档一样，输入相同的URL地址请求，如果请求体变化，会将原有的数据内容覆盖
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_doc/1
请求体内容为:

```
{
 "title":"华为1手机",
 "category":"华为1",
 "images":"http://www.gulixueyuan.com/hw.jpg",
 "price":4999.00
}
```
响应结果：
```
{
 "_index": "shopping",
 "_type": "_doc",
 "_id": "1",
 "_version"【版本】: 2,
 "result"【结果】: "updated", # updated 表示数据被更新
 "_shards": {
 "total": 2,
 "successful": 1,
 "failed": 0
 },
 "_seq_no": 2,
 "_primary_term": 2
}
```

##### 4.1.2.4 修改字段
修改数据时，也可以只修改某一给条数据的局部信息
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_update/1
请求体内容为：

```
{ 
 "doc": {
 "price":3000.00
 } 
}
```

##### 4.1.2.5 删除文档
删除一个文档不会立即从磁盘上移除，它只是被标记成已删除（逻辑删除）。
向 ES 服务器发 DELETE 请求 ：http://127.0.0.1:9200/shopping/_doc/1
删除成功，服务器响应结果：

```
{
 "_index": "shopping",
 "_type": "_doc",
 "_id": "1",
 "_version"【版本】: 4, #对数据的操作，都会更新版本
 "result"【结果】: "deleted", # deleted 表示数据被标记为删除
 "_shards": {
 "total": 2,
 "successful": 1,
 "failed": 0
 },
 "_seq_no": 4,
 "_primary_term": 2
}
```
如果删除一个并不存在的文档

```
{
 "_index": "shopping",
 "_type": "_doc",
 "_id": "1",
 "_version": 1,
 "result"【结果】: "not_found", # not_found 表示未查找到
 "_shards": {
 "total": 2,
 "successful": 1,
 "failed": 0
 },
 "_seq_no": 5,
 "_primary_term": 2
}
```

##### 4.1.2.6 条件删除文档
一般删除数据都是根据文档的唯一性标识进行删除，实际操作时，也可以根据条件对多条数据进行删除。
向 ES 服务器发 POST 请求 ：http://127.0.0.1:9200/shopping/_delete_by_query
请求体内容为：

```
{
    "query":{
      "match":{
         "price":4000.00
      }
    }
}
```
删除成功后，服务器响应结果：

```
{
 "took"【耗时】: 175,
 "timed_out"【是否超时】: false,
 "total"【总数】: 2,
 "deleted"【删除数量】: 2,
 "batches": 1,
 "version_conflicts": 0,
 "noops": 0,
 "retries": {
 "bulk": 0,
 "search": 0
 },
 "throttled_millis": 0,
 "requests_per_second": -1.0,
 "throttled_until_millis": 0,
 "failures": []
}
```

#### 4.1.3 映射操作
有了索引库，等于有了数据库中的 database。

接下来就需要建索引库(index)中的映射了，类似于数据库(database)中的表结构(table)。

创建数据库表需要设置字段名称，类型，长度，约束等；索引库也一样，需要知道这个类型下有哪些字段，每个字段有哪些约束信息，这就叫做**映射(mapping)**。

##### 4.1.3.1 创建映射
向 ES 服务器发 PUT 请求 ：http://127.0.0.1:9200/student/_mapping

请求体内容为：

```
{
    "properties": {
        "name": {
            "type": "text",
            "index": true
        },
        "sex": {
            "type": "text",
            "index": false
        },
        "age": {
            "type": "long",
            "index": false
        }
    }
}
```
服务器响应结果如下：

```
{
    "acknowledged": true
}
```
映射数据说明：
- 字段名：任意填写，下面指定许多属性，例如：title、subtitle、images、price
- type：类型，Elasticsearch 中支持的数据类型非常丰富，说几个关键的：
    - String 类型，又分两种：
        - text：可分词
        - keyword：不可分词，数据会作为完整字段进行匹配
    - Numerical：数值类型，分两类
        - 基本数据类型：long、integer、short、byte、double、float、half_float
        - 浮点数的高精度类型：scaled_float
    - Date：日期类型
    - Array：数组类型
    - Object：对象
- index：是否索引，默认为true，也就是说你不进行任何配置，所有字段都会被索引。
  true：字段会被索引，则可以用来进行搜索
  false：字段不会被索引，不能用来搜索
- store：是否将数据进行独立存储，默认为 false原始的文本会存储在_source 里面，默认情况下其他提取出来的字段都不是独立存储的，是从_source里面提取出来的。当然你也可以独立的存储某个字段，只要设置"store":true即可，获取独立存储的字段要比从_source 中解析快得多，但是也会占用更多的空间，所以要根据实际业务需求来设置。
- analyzer：分词器，这里的 ik_max_word 即使用ik分词器,后面会有专门的章节学习


##### 4.1.3.2 查看映射
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_mapping

##### 4.1.3.3 索引映射关联
向 ES 服务器发 PUT 请求 ：http://127.0.0.1:9200/student1

```
{
    "settings": {},
    "mappings": {
        "properties": {
            "name": {
                "type": "text",
                "index": true
            },
            "sex": {
                "type": "text",
                "index": false
            },
            "age": {
                "type": "long",
                "index": false
            }
        }
    }
}
```

#### 4.1.4 高级查询
Elasticsearch 提供了基于 JSON 提供完整的查询 DSL 来定义查询
定义数据 :

```
# POST /student/_doc/1001
{
"name":"zhangsan",
"nickname":"zhangsan",
 "sex":"男",
 "age":30
}
# POST /student/_doc/1002
{
"name":"lisi",
"nickname":"lisi",
 "sex":"男",
 "age":20
}
# POST /student/_doc/1003
{
"name":"wangwu",
 "nickname":"wangwu",
 "sex":"女",
 "age":40
}
# POST /student/_doc/1004
{
"name":"zhangsan1",
"nickname":"zhangsan1",
 "sex":"女",
 "age":50
}
# POST /student/_doc/1005
{
"name":"zhangsan2",
"nickname":"zhangsan2",
 "sex":"女",
 "age":30
}
```
##### 4.1.4.1 查询所有文档
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "query": {
 "match_all": {}
 }
}
# "query"：这里的 query 代表一个查询对象，里面可以有不同的查询属性
# "match_all"：查询类型，例如：match_all(代表查询所有)， match，term ， range 等等
# {查询条件}：查询条件会根据类型的不同，写法也有差异
```
服务器响应结果如下：

```
{
    "took【查询花费时间，单位毫秒】": 1116,
    "timed_out【是否超时】": false,
    "_shards【分片信息】": {
        "total【总数】": 1,
        "successful【成功】": 1,
        "skipped【忽略】": 0,
        "failed【失败】": 0
    },
    "hits【搜索命中结果】": {
        "total"【搜索条件匹配的文档总数】: {
            "value"【总命中计数的值】: 3,
            "relation"【计数规则】: "eq" # eq 表示计数准确， gte 表示计数不准确
        },
        "max_score【匹配度分值】": 1.0,
        "hits【命中结果集合】": [
 。。。
        }
    ]
}
}
```
##### 4.1.4.2 匹配查询
match 匹配类型查询，会把查询条件进行分词，然后进行查询，多个词条之间是 or 的关系。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "query": {
 "match": {
 "name":"zhangsan"
 }
 }
}
```

##### 4.1.4.3 字段匹配查询
multi_match 与 match 类似，不同的是它可以在多个字段中查询。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "query": {
 "multi_match": {
 "query": "zhangsan",
 "fields": ["name","nickname"]
 }
 }
}
```

##### 4.1.4.4 关键字精确查询
term 查询，精确的关键词匹配查询，不对查询条件进行分词。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "query": {
 "term": {
 "name": {
 "value": "zhangsan"
 }
 }
 }
}
```

##### 4.1.4.5 多关键字精确查询
terms 查询和 term 查询一样，但它允许你指定多值进行匹配。
如果这个字段包含了指定值中的任何一个值，那么这个文档满足条件，类似于 mysql 的 in。

向 ES 服务器发 GET 请求：http://127.0.0.1:9200/student/_search

```
{
 "query": {
 "terms": {
 "name": ["zhangsan","lisi"]
 }
 }
}
```

##### 4.1.4.6 指定查询字段
默认情况下，Elasticsearch 在搜索的结果中，会把文档中保存在_source 的所有字段都返回。
如果我们只想获取其中的部分字段，我们可以添加_source 的过滤.

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "_source": ["name","nickname"], 
 "query": {
 "terms": {
 "nickname": ["zhangsan"]
 }
 }
}
```

##### 4.1.4.7 过滤字段
我们也可以通过：
- includes：来指定想要显示的字段
- excludes：来指定不想要显示的字段

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "_source": {
 "includes": ["name","nickname"]
 }, 
 "query": {
 "terms": {
 "nickname": ["zhangsan"]
 }
 }
}
```

##### 4.1.4.8 组合查询
`bool`把各种其它查询通过`must`（必须）、`must_not`（必须不）、`should`（应该）的方式进行组合。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "name": "zhangsan"
                    }
                }
            ],
            "must_not": [
                {
                    "match": {
                        "age": "40"
                    }
                }
            ],
            "should": [
                {
                    "match": {
                        "sex": "男"
                    }
                }
            ]
        }
    }
}
```

##### 4.1.4.9 范围查询
range 查询找出那些落在指定区间内的数字或者时间。range 查询允许以下字符


操作符 | 说明
---|---
gt | 大于>
gte | 大于等于>=
lt | 小于<
lte | 小于等于<=

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search


```
{
    "query": {
        "range": {
            "age": {
                "gte": 30,
                "lte": 35
            }
        }
    }
}
```

##### 4.1.4.10 模糊查询
返回包含与搜索字词相似的字词的文档。
编辑距离是将一个术语转换为另一个术语所需的一个字符更改的次数。这些更改可以包括：
- 更改字符（box → fox）
- 删除字符（black → lack）
- 插入字符（sic → sick）
- 转置两个相邻字符（act → cat）
  为了找到相似的术语，fuzzy查询会在指定的编辑距离内创建一组搜索词的所有可能的变体或扩展。然后查询返回每个扩展的完全匹配。

通过 fuzziness 修改编辑距离。一般使用默认值AUTO，根据术语的长度生成编辑距离。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
    "query": {
        "fuzzy": {
            "title": {
                "value": "zhangsan"
            }
        }
    }
}
```

##### 4.1.4.11 单字段排序
sort 可以让我们按照不同的字段进行排序，并且通过 order 指定排序的方式。desc 降序，asc升序。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
    "query": {
        "match": {
            "name": "zhangsan"
        }
    },
    "sort": [
        {
            "age": {
                "order": "desc"
            }
        }
    ]
}
```

##### 4.1.4.12 多字段排序
假定我们想要结合使用age和_score进行查询，并且匹配的结果首先按照年龄排序，然后按照相关性得分排序。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
```
{
    "query": {
        "match_all": {}
    },
    "sort": [
        {
            "age": {
                "order": "desc"
            }
        },
        {
            "_score": {
                "order": "desc"
            }
        }
    ]
}
```

##### 4.1.4.13 高亮查询
在进行关键字搜索时，搜索出的内容中的关键字会显示不同的颜色，称之为高亮。
![image](.img/es_高亮示例.png)
Elasticsearch 可以对查询内容中的关键字部分，进行标签和样式(高亮)的设置。

在使用 match 查询的同时，加上一个 highlight 属性：
- pre_tags：前置标签
- post_tags：后置标签
- fields：需要高亮的字段
- title：这里声明 title

字段需要高亮，后面可以为这个字段设置特有配置，也可以空。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
```
{
    "query": {
        "match": {
            "name": "zhangsan"
        }
    },
    "highlight": {
        "pre_tags": "<font color='red'>",
        "post_tags": "</font>",
        "fields": {
            "name": {}
        }
    }
}
```

##### 4.1.4.14 分页查询
from：当前页的起始索引，默认从 0 开始。 from = (pageNum - 1) * size
size：每页显示多少条。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search
```
{
    "query": {
        "match_all": {}
    },
    "sort": [
        {
            "age": {
                "order": "desc"
            }
        }
    ],
    "from": 0,
    "size": 2
}
```

##### 4.1.4.15 聚合查询
聚合允许使用者对 es 文档进行统计分析，类似与关系型数据库中的 group by，当然还有很多其他的聚合，例如取最大值、平均值等等。
###### 对某个字段取最大值 max
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
    "aggs": {
        "max_age": {
            "max": {
                "field": "age"
            }
        }
    },
    "size": 0
}
```

###### 对某个字段取最小值 min
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "aggs":{
 "min_age":{
 "min":{"field":"age"}
 }
 },
 "size":0
}
```

###### 对某个字段求和 sum
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "aggs":{
 "sum_age":{
 "sum":{"field":"age"}
 }
 },
 "size":0
}
```

###### 对某个字段取平均值 avg
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "aggs":{
 "avg_age":{
 "avg":{"field":"age"}
 }
 },
 "size":0
}
```

###### 对某个字段的值进行去重之后再取总数
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "aggs":{
 "distinct_age":{
 "cardinality":{"field":"age"}
 }
 },
 "size":0
}
```

###### State 聚合
stats 聚合，对某个字段一次性返回 count，max，min，avg 和 sum 五个指标。

向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search

```
{
 "aggs":{
 "stats_age":{
 "stats":{"field":"age"}
 }
 },
 "size":0
}
```

###### 桶聚合查询
桶聚和相当于 sql 中的 group by 语句。

**terms 聚合，分组统计：**
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search


```
{
 "aggs":{
 "age_groupby":{
 "terms":{"field":"age"}
 }
 },
 "size":0
}
```

**在 terms 分组下再进行聚合：**
向 ES 服务器发 GET 请求 ：http://127.0.0.1:9200/student/_search


```
{
 "aggs":{
 "age_groupby":{
 "terms":{"field":"age"}
 }
 },
 "size":0
}
```

### 4.2 Java API 操作
Elasticsearch 软件是由 Java 语言开发的，所以也可以通过 Java API 的方式对 Elasticsearch服务进行访问。

==注意：具体代码在另一个ESLearn的仓库中。==

#### 4.2.1 索引
ES 服务器正常启动后，可以通过 Java API 客户端对象对 ES 索引进行操作。
##### 4.2.1.1 创建索引

```
// 创建索引 - 请求对象
CreateIndexRequest request = new CreateIndexRequest("user");
// 发送请求，获取响应
CreateIndexResponse response = client.indices().create(request, 
RequestOptions.DEFAULT);
boolean acknowledged = response.isAcknowledged();
// 响应状态
System.out.println("操作状态 = " + acknowledged);
```

##### 4.2.1.2 查看索引

```
// 查询索引 - 请求对象
GetIndexRequest request = new GetIndexRequest("user");
// 发送请求，获取响应
GetIndexResponse response = client.indices().get(request, 
RequestOptions.DEFAULT);
System.out.println("aliases:"+response.getAliases());
System.out.println("mappings:"+response.getMappings());
System.out.println("settings:"+response.getSettings());
```

##### 4.2.1.3 删除索引

```
// 删除索引 - 请求对象
DeleteIndexRequest request = new DeleteIndexRequest("user");
// 发送请求，获取响应
AcknowledgedResponse response = client.indices().delete(request, 
RequestOptions.DEFAULT);
// 操作结果
System.out.println("操作结果 ： " + response.isAcknowledged());
```

#### 4.2.2 文档
##### 4.2.2.1 新增文档
创建数据模型

```
public class User {
    private String name;
    private String sex;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```
创建数据，添加到文档中
```
// 新增文档 - 请求对象
IndexRequest request = new IndexRequest();
// 设置索引及唯一性标识
request.index("user").id("1001");
// 创建数据对象
User user = new User();
user.setName("zhangsan");
user.setAge(30);
user.setSex("男");
ObjectMapper objectMapper = new ObjectMapper();
String productJson = objectMapper.writeValueAsString(user);
// 添加文档数据，数据格式为 JSON 格式
request.source(productJson,XContentType.JSON);
// 客户端发送请求，获取响应对象
IndexResponse response = client.index(request, RequestOptions.DEFAULT);
////3.打印结果信息
System.out.println("_index:" + response.getIndex());
System.out.println("_id:" + response.getId());
System.out.println("_result:" + response.getResult()); 
```

##### 4.2.2.2 修改文档
```
// 修改文档 - 请求对象
UpdateRequest request = new UpdateRequest();
// 配置修改参数
request.index("user").id("1001");
// 设置请求体，对数据进行修改
request.doc(XContentType.JSON, "sex", "女");
// 客户端发送请求，获取响应对象
UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
System.out.println("_index:" + response.getIndex());
System.out.println("_id:" + response.getId());
System.out.println("_result:" + response.getResult());
```

##### 4.2.2.3 查询文档
```
//1.创建请求对象
GetRequest request = new GetRequest().index("user").id("1001");
//2.客户端发送请求，获取响应对象
GetResponse response = client.get(request, RequestOptions.DEFAULT);
////3.打印结果信息
System.out.println("_index:" + response.getIndex());
System.out.println("_type:" + response.getType());
System.out.println("_id:" + response.getId());
System.out.println("source:" + response.getSourceAsString());
```

##### 4.2.2.4 删除文档
```
//创建请求对象
DeleteRequest request = new DeleteRequest().index("user").id("1");
//客户端发送请求，获取响应对象
DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);
//打印信息
System.out.println(response.toString());
```

##### 4.2.2.5 批量操作
```
//创建批量新增请求对象
BulkRequest request = new BulkRequest();
request.add(new 
IndexRequest().index("user").id("1001").source(XContentType.JSON, "name", 
"zhangsan"));
request.add(new 
IndexRequest().index("user").id("1002").source(XContentType.JSON, "name", 
"lisi"));
request.add(new 
IndexRequest().index("user").id("1003").source(XContentType.JSON, "name", 
"wangwu"));
//客户端发送请求，获取响应对象
BulkResponse responses = client.bulk(request, RequestOptions.DEFAULT);
//打印结果信息
System.out.println("took:" + responses.getTook());
System.out.println("items:" + responses.getItems());
```

##### 4.2.2.6 批量删除
```
//创建批量删除请求对象
BulkRequest request = new BulkRequest();
request.add(new DeleteRequest().index("user").id("1001"));
request.add(new DeleteRequest().index("user").id("1002"));
request.add(new DeleteRequest().index("user").id("1003"));
//客户端发送请求，获取响应对象
BulkResponse responses = client.bulk(request, RequestOptions.DEFAULT);
//打印结果信息
System.out.println("took:" + responses.getTook());
System.out.println("items:" + responses.getItems());
```

#### 4.2.3 高级查询
##### 4.2.3.1 请求体查询
###### 查询所有索引数据
```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
// 查询所有数据
sourceBuilder.query(QueryBuilders.matchAllQuery());
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### term 查询，查询条件为关键字

```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.query(QueryBuilders.termQuery("age", "30"));
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### 分页查询
```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.query(QueryBuilders.matchAllQuery());
// 分页查询
// 当前页其实索引(第一条数据的顺序号)，from
sourceBuilder.from(0);
// 每页显示多少条 size
sourceBuilder.size(2);
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### 数据排序
```
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.query(QueryBuilders.matchAllQuery());
// 排序
sourceBuilder.sort("age", SortOrder.ASC);
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### 过滤字段

```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.query(QueryBuilders.matchAllQuery());
//查询字段过滤
String[] excludes = {};
String[] includes = {"name", "age"};
sourceBuilder.fetchSource(includes, excludes);
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### Bool 查询

```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
// 必须包含
boolQueryBuilder.must(QueryBuilders.matchQuery("age", "30"));
// 一定不含
boolQueryBuilder.mustNot(QueryBuilders.matchQuery("name", "zhangsan"));
// 可能包含
boolQueryBuilder.should(QueryBuilders.matchQuery("sex", "男"));
sourceBuilder.query(boolQueryBuilder);
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### 范围查询

```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age");
// 大于等于
rangeQuery.gte("30");
// 小于等于
rangeQuery.lte("40");
sourceBuilder.query(rangeQuery);
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### 模糊查询

```
// 创建搜索请求对象
SearchRequest request = new SearchRequest();
request.indices("student");
// 构建查询的请求体
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.query(QueryBuilders.fuzzyQuery("name","zhangsan").fuzziness(Fuzziness.ONE));
request.source(sourceBuilder);
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
// 查询匹配
SearchHits hits = response.getHits();
System.out.println("took:" + response.getTook());
System.out.println("timeout:" + response.isTimedOut());
System.out.println("total:" + hits.getTotalHits());
System.out.println("MaxScore:" + hits.getMaxScore());
System.out.println("hits========>>");
for (SearchHit hit : hits) {
//输出每条查询的结果信息
System.out.println(hit.getSourceAsString());
}
System.out.println("<<========");
```

###### 高亮查询

```
// 高亮查询
SearchRequest request = new SearchRequest().indices("student");
//2.创建查询请求体构建器
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
//构建查询方式：高亮查询
TermsQueryBuilder termsQueryBuilder = 
QueryBuilders.termsQuery("name","zhangsan");
//设置查询方式
sourceBuilder.query(termsQueryBuilder);
//构建高亮字段
HighlightBuilder highlightBuilder = new HighlightBuilder();
highlightBuilder.preTags("<font color='red'>");//设置标签前缀
highlightBuilder.postTags("</font>");//设置标签后缀
highlightBuilder.field("name");//设置高亮字段
//设置高亮构建对象
sourceBuilder.highlighter(highlightBuilder);
//设置请求体
request.source(sourceBuilder);
//3.客户端发送请求，获取响应对象
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
//4.打印响应结果
SearchHits hits = response.getHits();
System.out.println("took::"+response.getTook());
System.out.println("time_out::"+response.isTimedOut());
System.out.println("total::"+hits.getTotalHits());
System.out.println("max_score::"+hits.getMaxScore());
System.out.println("hits::::>>");
for (SearchHit hit : hits) {
String sourceAsString = hit.getSourceAsString();
System.out.println(sourceAsString);
//打印高亮结果
Map<String, HighlightField> highlightFields = hit.getHighlightFields();
System.out.println(highlightFields);
}
System.out.println("<<::::");
```

###### 聚合查询
- 最大年龄

```
// 高亮查询
SearchRequest request = new SearchRequest().indices("student");
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.aggregation(AggregationBuilders.max("maxAge").field("age"));
//设置请求体
request.source(sourceBuilder);
//3.客户端发送请求，获取响应对象
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
//4.打印响应结果
SearchHits hits = response.getHits();
System.out.println(response);
```

- 分组统计
```
// 高亮查询
SearchRequest request = new SearchRequest().indices("student");
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
sourceBuilder.aggregation(AggregationBuilders.terms("age_groupby").field("age"));
//设置请求体
request.source(sourceBuilder);
//3.客户端发送请求，获取响应对象
SearchResponse response = client.search(request, RequestOptions.DEFAULT);
//4.打印响应结果
SearchHits hits = response.getHits();
System.out.println(response);
```








# 附录：
## 问题解决：
1.Elasticsearch 是使用 java 开发的，且 7.8 版本的 ES 需要 JDK 版本 1.8 以上，默认安装包带有 jdk 环境，如果系统配置 JAVA_HOME，那么使用系统默认的 JDK，如果没有配置使用自带的 JDK，一般建议使用系统配置的 JDK。

2.双击启动窗口闪退，通过路径访问追踪错误，如果是“空间不足”，请修改
config/jvm.options 配置文件。
```
# 设置 JVM 初始内存为 1G。此值可以设置与-Xmx 相同，以避免每次垃圾回收完成后 JVM 重新分配内存
# Xms represents the initial size of total heap space
# 设置 JVM 最大可用内存为 1G
# Xmx represents the maximum size of total heap space
-Xms1g
-Xmx1g
```

