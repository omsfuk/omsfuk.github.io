# MySQL 全文检索

MySQL 5.7.6 中内置了全文索引插件来处理它们：n-gram parser。
之前也有全文索引插件，但是只支持Latin语系。


## 准备
首先在`my.ng`中设置
```
ngram_token_size=2
```

实例

```sql
CREATE TABLE book (
    id AUTO_INCREMENT NOT NULL PRIMARY KEY,
    title VARCHAR(200),
    FULLTEXT (title) WITH PARSER ngram
) ENGINE=InnoDB CHARACTER SET utf8mb4;
```

指定全文检索表源
```
SETGLOBAL innodb_ft_aux_table="library/book"
```

## 自然语言模式查询
查看分词信息
```
SELECT * FROM information_schema.INNODB_FT_INDEX_CACHE LIMIT 20,10;
```

查看匹配比率
```
SELECT id, MATCH (title,body) AGAINST ('数据库' IN NATURAL LANGUAGE MODE) AS score FROM articles;
```

## 布尔模式查询

#### 匹配既有管理又有数据库的记录，
```
SELECT * FROM articles WHERE MATCH (title,body) AGAINST ('+数据库 +管理' IN BOOLEAN MODE);
```
#### 匹配有数据库，但是没有管理的记录，
```
SELECT * FROM articles WHERE MATCH (title,body) AGAINST ('+数据库 -管理' IN BOOLEAN MODE);
```
#### 匹配MySQL，但是把数据库的相关性降低，
```
SELECT * FROM articles WHERE MATCH (title,body) AGAINST ('>数据库 +MySQL' INBOOLEAN MODE);
```

## 查询扩展模式
比如要搜索数据库，那么MySQL，oracle，DB2也都将会被搜索到，
```
SELECT * FROM articles WHERE MATCH (title,body) AGAINST ('数据库' WITH QUERY EXPANSION);
```
