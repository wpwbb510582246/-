# 学习笔记(ElasticSearch)

## 1、常用API

### 1.1 查询

#### 1.1.1 term查询

term查询是单词级别的查询，，一般用于精确查询，通常用于结构化的数据，比如：number、date、keyword等，而不是text，不会对内容进行分词；而全文查询一般需对文本内容进行分词，且数据类型需要是text。

##### 1.1.1.1 term query 精准匹配查询

```json
// 查找号码为23的球员
POST nba/_search
{
    "query": {
        "term": {
            "jerseyNo": "23"
        }
    }
}
```

##### 1.1.1.2 exsit Query 在特定的字段中查找⾮空值的⽂档

```json
// 查找队名⾮空的球员
POST nba/_search
{
    "query": {
        "exists": {
            "field": "teamNameEn"
        }
    }
}
```

##### 1.1.1.3 prefifix Query 查找包含带有指定前缀term的⽂档

```json
// 查找队名以Rock开头的球员
POST nba/_search
{
    "query": {
        "prefix": {
            "teamNameEn": "Rock"
        }
    }
}
```

##### 1.1.1.4 wildcard Query ⽀持通配符查询，*表示任意字符，?表示任意单个字符

```json
// 查找⽕箭队的球员
POST nba/_search
{
    "query": {
        "wildcard": {
            "teamNameEn": "Ro*s"
        }
    }
}
```

##### 1.1.1.5 regexp Query 正则表达式查询

```json
// 查找⽕箭队的球员
POST nba/_search
{
    "query": {
        "regexp": {
            "teamNameEn": "Ro.*s"
        }
    }
}
```

##### 1.1.1.6 ids query

```json
// 查找id为1和2的球员
POST nba/_search
{
    "query": {
        "ids": {
            "values": [
                1,
                2
            ]
        }
    }
}
```

#### 1.1.2 范围查询

```json
// 查找在nba打了2年到10年以内的球员
POST nba/_search
{
    "query": {
        "range": {
            "playYear": {
                "gte": 2,
                "lte": 10
            }
        }
    }
}
```

```json
// 查找1980年到1999年出⽣的球员
POST nba/_search
{
    "query": {
        "range": {
            "birthDay": {
                "gte": "01/01/1999",
                "lte": "2022",
                "format": "dd/MM/yyyy||yyyy"
            }
        }
    }
}
```

#### 1.1.3 布尔查询

| **type** | **description**              |
| :------- | :--------------------------- |
| must     | 必须出现在匹配⽂档中         |
| filter   | 必须出现在⽂档中，但是不打分 |
| must_not | 不能出现在⽂档中             |
| should   | 应该出现在⽂档中             |

##### 1.1.3.1 must 

```json
// 查找名字叫做James的球员
POST /nba/_search
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "displayNameEn": "james"
                    }
                }
            ]
        }
    }
}
```

##### 1.1.3.2 filter

效果同must，但是不打分

```json
// 查找名字叫做James的球员
POST /nba/_search
{
    "query": {
        "bool": {
            "filter": [
                {
                    "match": {
                        "displayNameEn": "james"
                    }
                }
            ]
        }
    }
}
```

#####  1.1.3.3 must_not

```json
// 查找名字叫做James的⻄部球员
POST /nba/_search
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "displayNameEn": "james"
                    }
                }
            ],
            "must_not": [
                {
                    "term": {
                        "teamConferenceEn": {
                            "value": "Eastern"
                        }
                    }
                }
            ]
        }
    }
}
```

##### 1.1.3.4 should

即使匹配不到也返回，只是评分不同

```json
// 查找名字叫做James的打球时间应该在11到20年⻄部球员)
POST /nba/_search
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "displayNameEn": "james"
                    }
                }
            ],
            "must_not": [
                {
                    "term": {
                        "teamConferenceEn": {
                            "value": "Eastern"
                        }
                    }
                }
            ],
            "should": [
                {
                    "range": {
                        "playYear": {
                            "gte": 11,
                            "lte": 20
                        }
                    }
                }
            ]
        }
    }
}
```

如果minimum_should_match=1，则变成要查出名字叫做James的打球时间在11到20年⻄部球员

```json
// 查出名字叫做James的打球时间在11到20年⻄部球员
POST /nba/_search
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "displayNameEn": "james"
                    }
                }
            ],
            "must_not": [
                {
                    "term": {
                        "teamConferenceEn": {
                            "value": "Eastern"
                        }
                    }
                }
            ],
            "should": [
                {
                    "range": {
                        "playYear": {
                            "gte": 11,
                            "lte": 20
                        }
                    }
                }
            ],
            "minimum_should_match": 1
        }
    }
}
```

#### 1.1.4 排序查询

```json
// ⽕箭队中按打球时间从⼤到⼩，如果年龄相同则按照身⾼从⾼到低排序的球员
POST nba/_search
{
    "query": {
        "match": {
            "teamNameEn": "Rockets"
        }
    },
    "sort": [
        {
            "playYear": {
                "order": "desc"
            }
        },
        {
            "heightValue": {
                "order": "asc"
            }
        }
    ]
}
```

#### 1.1.5 聚合查询

##### 1.1.5.1 指标聚合

指标聚合：对⼀个数据集求最⼤、最⼩、和、平均值等指标的聚合。

- max、min、sum、avg：

  ```json
  // 求出⽕箭队球员的平均年龄
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "avgAge": {
              "avg": {
                  "field": "age"
              }
          }
      },
      "size": 0
  }
  ```

- value_count：统计⾮空字段的⽂档数

  ```json
  // 求出⽕箭队中球员打球时间不为空的数量
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "countPlayerYear": {
              "value_count": {
                  "field": "playYear"
              }
          }
      },
      "size": 0
  }
  ```

- _count(不属于聚合查询)：

  ```json
  // 查出⽕箭队有多少名球员
  POST nba/_count
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      }
  }
  ```

- cardinality：去重计数

  ```json
  // 查出⽕箭队中年龄不同的数量
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "counAget": {
              "cardinality": {
                  "field": "age"
              }
          }
      },
      "size": 0
  }
  ```

- stats：统计count、max、min、avg、sum5个值

  ```json
  // 查出⽕箭队球员的年龄stats
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "statsAge": {
              "stats": {
                  "field": "age"
              }
          }
      },
      "size": 0
  }
  ```

- extended stats：⽐stats多4个统计结果(平⽅和、⽅差、标准差、平均值加/减两个标准差的区间)

  ```json
  // 查出⽕箭队球员的年龄extend stats
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "extendStatsAge": {
              "extended_stats": {
                  "field": "age"
              }
          }
      },
      "size": 0
  }
  ```

- percentiles：占⽐百分位对应的值统计，默认返回[ 1, 5, 25, 50, 75, 95, 99 ]分位上的值

  ```json
  // 查出⽕箭的球员的年龄占⽐
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "pecentAge": {
              "percentiles": {
                  "field": "age"
              }
          }
      },
      "size": 0
  }
  ```

  ```json
  // 查出⽕箭的球员的年龄占⽐(指定分位值)
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "percentAge": {
              "percentiles": {
                  "field": "age",
                  "percents": [
                      20,
                      50,
                      75
                  ]
              }
          }
      },
      "size": 0
  }
  ```

##### 1.1.5.2 桶聚合

桶聚合：对查询出的数据进⾏分组group by，再在组上进⾏指标聚合。

- terms aggregation：根据字段项分组聚合

  ```json
  // ⽕箭队根据年龄进⾏分组，分组信息通过年龄从⼤到⼩排序 (通过指定字段)
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "aggsAge": {
              "terms": {
                  "field": "age",
                  "size": 10,
                  "order": {
                      "_key": "desc"
                  }
              }
          }
      },
      "size": 0
  }
  ```

  ```json
  // ⽕箭队根据年龄进⾏分组，分组信息通过⽂档数从⼤到⼩排序 (通过⽂档数)
  POST /nba/_search
  {
      "query": {
          "term": {
              "teamNameEn": {
                  "value": "Rockets"
              }
          }
      },
      "aggs": {
          "aggsAge": {
              "terms": {
                  "field": "age",
                  "size": 10,
                  "order": {
                      "_count": "desc"
                  }
              }
          }
      },
      "size": 0
  }
  ```

  ```json
  // 每⽀球队按该队所有球员的平均年龄进⾏分组排序 (通过分组指标值)
  POST /nba/_search
  {
      "aggs": {
          "aggsTeamName": {
              "terms": {
                  "field": "teamNameEn",
                  "size": 30,
                  "order": {
                      "avgAge": "desc"
                  }
              },
              "aggs": {
                  "avgAge": {
                      "avg": {
                          "field": "age"
                      }
                  }
              }
          }
      },
      "size": 0
  }
  ```

- 筛选分组聚合：

  ```json
  // 湖⼈和⽕箭队按球队平均年龄进⾏分组排序 (指定值列表)
  POST /nba/_search
  {
      "aggs": {
          "aggsTeamName": {
              "terms": {
                  "field": "teamNameEn",
                  "include": [
                      "Lakers",
                      "Rockets",
                      "Warriors"
                  ],
                  "exclude": [
                      "Warriors"
                  ],
                  "size": 30,
                  "order": {
                      "avgAge": "desc"
                  }
              },
              "aggs": {
                  "avgAge": {
                      "avg": {
                          "field": "age"
                      }
                  }
              }
          }
      },
      "size": 0
  }
  ```

  ```json
  // 湖⼈和⽕箭队按球队平均年龄进⾏分组排序 (正则表达式匹配值)
  POST /nba/_search
  {
      "aggs": {
          "aggsTeamName": {
              "terms": {
                  "field": "teamNameEn",
                  "include": "Lakers|Ro.*|Warriors.*",
                  "exclude": "Warriors",
                  "size": 30,
                  "order": {
                      "avgAge": "desc"
                  }
              },
              "aggs": {
                  "avgAge": {
                      "avg": {
                          "field": "age"
                      }
                  }
              }
          }
      },
      "size": 0
  }
  ```

- range aggregation：范围分组聚合

  ```json
  // NBA球员年龄按20,20-35,35这样分组
  POST /nba/_search
  {
      "aggs": {
          "ageRange": {
              "range": {
                  "field": "age",
                  "ranges": [
                      {
                          "to": 20
                      },
                      {
                          "from": 20,
                          "to": 35
                      },
                      {
                          "from": 35
                      }
                  ]
              }
          }
      },
      "size": 0
  }
  ```

  ```json
  // NBA球员年龄按20,20-35,35这样分组 (起别名)
  POST /nba/_search
  {
      "aggs": {
          "ageRange": {
              "range": {
                  "field": "age",
                  "ranges": [
                      {
                          "to": 20,
                          "key": "A"
                      },
                      {
                          "from": 20,
                          "to": 35,
                          "key": "B"
                      },
                      {
                          "from": 35,
                          "key": "C"
                      }
                  ]
              }
          }
      },
      "size": 0
  }
  ```

- date range aggregation：时间范围分组聚合

  ```json
  // NBA球员按出⽣年⽉分组
  POST /nba/_search
  {
      "aggs": {
          "birthDayRange": {
              "date_range": {
                  "field": "birthDay",
                  "format": "MM-yyy",
                  "ranges": [
                      {
                          "to": "01-1989"
                      },
                      {
                          "from": "01-1989",
                          "to": "01-1999"
                      },
                      {
                          "from": "01-1999",
                          "to": "01-2009"
                      },
                      {
                          "from": "01-2009"
                      }
                  ]
              }
          }
      },
      "size": 0
  }
  ```

- date histogram aggregation：时间柱状图聚合，按天、⽉、年等进⾏聚合统计。可按 year (1y), quarter (1q), month (1M), week (1w), day(1d), hour (1h), minute (1m), second (1s)间隔聚合

  ```json
  // NBA球员按出⽣年分组
  POST /nba/_search
  {
      "aggs": {
          "birthday_aggs": {
              "date_histogram": {
                  "field": "birthDay",
                  "format": "yyyy",
                  "interval": "year"
              }
          }
      },
      "size": 0
  }
  ```

#### 1.1.6 **query_string**查询

query_string 查询：直接⽤lucene查询语法写⼀个查询串进⾏查询，ES中接到请求后，通过查询解析器,解析查询串⽣成对应的查询。

- 指定单个字段查询：

  ```json
  POST /nba/_search
  {
      "query": {
          "query_string": {
              "default_field": "displayNameEn",
              "query": "james OR curry"
          }
      },
      "size": 100
  }
  ```

- 指定多个字段查询

  ```json
  POST /nba/_search
  {
      "query": {
          "query_string": {
              "fields": [
                  "displayNameEn",
                  "teamNameEn"
              ],
              "query": "James AND Rockets"
          }
      },
      "size": 100
  }
  ```

#### 1.1.7 高亮查询

```json
// 默认高亮查询
POST /nba_latest/_search
{
    "query": {
        "match": {
            "displayNameEn": "james"
        }
    },
    "highlight": {
        "fields": {
            "displayNameEn": {}
        }
    }
}
```

```json
// ⾃定义⾼亮查询
POST /nba_latest/_search
{
    "query": {
        "match": {
            "displayNameEn": "james"
        }
    },
    "highlight": {
        "fields": {
            "displayNameEn": {
                "pre_tags": [
                    "<h1>"
                ],
                "post_tags": [
                    "</h1>"
                ]
            }
        }
    }
}
```

#### 1.1.8 查询建议

查询建议是为了给⽤户提供更好的搜索体验，包括：词条检查，⾃动补全。

| 字段         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| text         | 指定搜索文本                                                 |
| field        | 获取建议词的搜索字段                                         |
| analyzer     | 指定分词器                                                   |
| size         | 每个词返回的最大建议词数                                     |
| sort         | 如何对建议词进行排序，可用选项：score(先按评分进行排序，再按文档频率排，最后按term顺序排)、frequency(先按文档频率排，再按评分排，最后按term顺序排)。 |
| Suggest_mode | 建议模式，控制提供建议词的方式：missing(仅在搜索的词项在索引中不存在时才提供建议词，默认值)、popular(仅建议文档频率比搜索词项高的词)、always(总是提供匹配的建议词)。 |

##### 1.1.8.1 term suggester

term 词条建议器，对给输⼊的⽂本进⾏分词，为每个分词提供词项建议。

```json
POST /nba_latest/_search
{
    "suggest": {
        "my-suggestion": {
            "text": "jamse hardne",
            "term": {
                "suggest_mode": "missing",
                "field": "displayNameEn"
            }
        }
    }
}
```

##### 1.1.8.2 phrase suggester

phrase 短语建议，在term的基础上，会考量多个term之间的关系，⽐如是否同时出现在索引的原⽂⾥，相邻程度，以及词频等。

```json
POST /nba_latest/_search
{
    "suggest": {
        "my-suggestion": {
            "text": "jamse harden",
            "phrase": {
                "field": "displayNameEn"
            }
        }
    }
}
```

##### 1.1.8.3 completion suggester

completion完成建议，用于自动补全。

```json
POST /nba_latest/_search
{
    "suggest": {
        "my-suggestion": {
            "text": "Miam",
            "completion": {
                "field": "teamCityEn"
            }
        }
    }
}
```



### 1.2 索引

#### 1.2.1 索引别名

别名的作用：在开发中，随着业务需求的迭代，较⽼的业务逻辑就要⾯临更新甚⾄是重构，⽽对于es来说，为了适应新的业务逻辑，可能就要对原有的索引做⼀些修改，⽐如对某些字段做调整，甚⾄是重建索引。⽽做这些操作的时候，可能会对业务造成影响，甚⾄是停机调整等问题。由此，es提供了索引别名来解决这些问题。 索引别名就像⼀个快捷⽅式或是软连接，可以指向⼀个或多个索引，也可以给任意⼀个需要索引名的API来使⽤。别名的应⽤为程序提供了极⼤地灵活性。

##### 1.2.1.1 索引别名的用法

- 查询别名：

  ```json
  // 查询全部索引别名
  GET /_alias
  // 查询nba索引别名
  GET /nba/_alias
  ```

- 新增别名：

  ```json
  PUT /nba/_alias/nba_v1.1
  ```

  ```json
  POST /_aliases
  {
      "actions": [
          {
              "add": {
                  "index": "nba",
                  "alias": "nba_v1.0"
              }
          }
      ]
  }
  ```

- 删除别名：

  ```json
  DELETE /nba/_alias/nba_v1.1
  ```

  ```
  POST /_aliases
  {
      "actions": [
          {
              "remove": {
                  "index": "nba",
                  "alias": "nba_v1.0"
              }
          }
      ]
  }
  ```

- 重命名：

  ```json
  POST /_aliases
  {
      "actions": [
          {
              "remove": {
                  "index": "nba",
                  "alias": "nba_v1.0"
              }
          },
          {
              "add": {
                  "index": "nba",
                  "alias": "nba_v2.0"
              }
          }
      ]
  }
  ```

#### 1.2.2 重建索引

背景：Elasticsearch是⼀个实时的分布式搜索引擎，为⽤户提供搜索服务，当我们决定存储某种数据时，在创建索引的时候需要将数据结构完整确定下来，于此同时索引的设定和很多固定配置将⽤不能改变。当需要改变数据结构时，就需要重新建⽴索引，为此，Elastic团队提供了很多辅助⼯具帮助开发⼈员进⾏重建索引。

步骤：

1. nba取⼀个别名nba_latest, nba_latest作为对外使⽤；

2. 新增⼀个索引nba_20220101，结构复制于nba索引，根据业务要求修改字段；

   ```json
   PUT /nba_20220101
   {
       "mappings": {
           "properties": {
               "age": {
                   "type": "integer"
               },
               "birthDay": {
                   "type": "date"
               },
               "birthDayStr": {
                   "type": "keyword"
               },
             ...
           }
       }
   }
   ```

3. 将nba数据同步到nba_20220101；

   ```json
   // 同步等待，接⼝将会在 reindex 结束后返回
   POST /_reindex
   {
       "source": {
           "index": "nba"
       },
       "dest": {
           "index": "nba_20220101"
       }
   }
   ```

   ```json
   // 异步执⾏，如果reindex时间过⻓，建议加上wait_for_completion=false的参数条件，这样reindex将直接返回 taskId
   POST /_reindex?wait_for_completion=false
   {
       "source": {
           "index": "nba"
       },
       "dest": {
           "index": "nba_20220101"
       }
   }
   ```

4. 给nba_20220101添加别名nba_latest，删除nba别名nba_latest；

   ```json
   POST /_aliases
   {
       "actions": [
           {
               "add": {
                   "index": "nba_20220101",
                   "alias": "nba_latest"
               }
           },
           {
               "remove": {
                   "index": "nba",
                   "alias": "nba_latest"
               }
           }
       ]
   }
   ```

5. 删除nba索引；

   ```json
   DELETE /nba
   ```

   

6. 通过别名访问新索引。

   ```json
   POST /nba_latest/_search
   {
       "query": {
           "match": {
               "displayNameEn": "james"
           }
       }
   }
   ```