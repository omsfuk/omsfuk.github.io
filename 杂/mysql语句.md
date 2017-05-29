# MySQL语句集锦
## 删除唯一约束

因为唯一约束会默认设为索引，所以要先删除索引，然后修改字段。
```
alter table test drop index name;
alter table test change name name char(255) not null;
```
## 添加唯一约束
```
create unique index UserNameIndex on note(userid, url);
```
