# 显式Mapping设置与常见参数介绍
## 课程Demos

### 1、put 指定mapping格式
```


PUT index—name
{
    "mappings" : {
    ...
    }
}

```
### 2、给不需要index的字段设置 index 为 false，

```
DELETE users
PUT users
{
    "mappings" : {
      "properties" : {
        "firstName" : {
          "type" : "text"
        },
        "lastName" : {
          "type" : "text"
        },
        "mobile" : {
          "type" : "text",
          "index": false
        }
      }
    }
}

# 写入一份带 mobile的数据
PUT users/_doc/1
{
  "firstName":"Ruan",
  "lastName": "Yiming",
  "mobile": "12345678"
}

# 格局mobile 字段匹配，查询数据，
POST /users/_search
{
  "query": {
    "match": {
      "mobile":"12345678"
    }
  }
}

结论： 无法查询，报错为查询的字段没有index。


#3、设定Null_value，当我们有些字段为空值时，我们需要在mapping 中定义 "NULL", 这个看起来是字符串，但是在写进去真实数据时，需要显示的设置为null，返回的就是null，

DELETE users
PUT users
{
    "mappings" : {
      "properties" : {
        "firstName" : {
          "type" : "text"
        },
        "lastName" : {
          "type" : "text"
        },
        "mobile" : {
          "type" : "keyword",
          "null_value": "NULL"
        }

      }
    }
}

### 显示指定我们的字段为null
PUT users/_doc/1
{
  "firstName":"Ruan",
  "lastName": "Yiming",
  "mobile": null
}

###。没有对mobile 字段写入数据。
PUT users/_doc/2
{
  "firstName":"Ruan2",
  "lastName": "Yiming2"
}

### 查询 mobile 为 null 的文档
GET users/_search
{
  "query": {
    "match": {
      "mobile":"NULL"
    }
  }

}



#设置 Copy to
DELETE users
PUT users
{
  "mappings": {
    "properties": {
      "firstName":{
        "type": "text",
        "copy_to": "fullName"
      },
      "lastName":{
        "type": "text",
        "copy_to": "fullName"
      }
    }
  }
}
PUT users/_doc/1
{
  "firstName":"Ruan",
  "lastName": "Yiming"
}

GET users/_search?q=fullName:(Ruan Yiming)

POST users/_search
{
  "query": {
    "match": {
       "fullName":{
        "query": "Ruan Yiming",
        "operator": "and"
      }
    }
  }
}


#数组类型
PUT users/_doc/1
{
  "name":"onebird",
  "interests":"reading"
}

PUT users/_doc/1
{
  "name":"twobirds",
  "interests":["reading","music"]
}

POST users/_search
{
  "query": {
		"match_all": {}
	}
}

GET users/_mapping

```


## 补充阅读
- Mapping Parameters https://www.elastic.co/guide/en/elasticsearch/reference/7.1/mapping-params.html
