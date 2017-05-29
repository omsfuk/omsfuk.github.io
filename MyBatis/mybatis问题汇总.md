# MyBatis问题汇总
# 允许多条语句
1. 修改数据库连接参数加上allowMultiQueries=true，
```
jdbc:mysql://xx.xx.xx:3306/xxxxx?characterEncoding=utf-8&autoReconnect=true&failOverReadOnly=false&allowMultiQueries=true
```
2. 直接写多条语句，用`;`隔开即可
```
<delete id="deleteUserById" parameterType="String">
    delete from sec_user_role where userId=#{id};
    delete from sec_user where id=#{id};
</delete>
```

# update后from中不能有select的解决方案
利用自动创建的临时表。
假设原来是这么写
```
delete from directory where userid = 1 and path regexp concat('^', (select path from directory where id = #{id}));

```
可以改为
```
delete from directory where userid = 1 and path regexp concat('^', (select path from (select * from directory)t where id = #{id}));
```
其中创建了名为`t`的临时表

## MyBatis处理xml中的注释出问题
删掉注释，不然可能会提示parameter index of ...一堆东西
