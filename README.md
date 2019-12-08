## 所以为什么README我要放一些好用的tips:

[直接修改表中全部字段的charset](#直接修改表中全部字段的charset.)

#### 直接修改表中全部字段的charset.

```sql
alter table `tablename` convert to charset utf8;  
```

#### 使用go test时，如果不设置GOMUDULE111环境变量，将不使用mod
