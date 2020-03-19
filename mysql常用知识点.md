# mysql常用知识点
## 修改表结构
### 在某个字段之后添加表字段  默认值 注释
ALTER TABLE goods_goods ADD isTrainCourse INT ( 1 ) DEFAULT 0 COMMENT '是否是实训课程 (0: 不是, 1: 是)' AFTER isExam;
## 常用mysql函数
### CONCAT_WS(separator,str1,str2,…)函数
SELECT CONCAT_WS(',','First name',NULL,'Last Name');返回结果为
First name,Last Name
### IF(expr1,expr2,expr3)函数
IF("document"!=ttcl.train_lesson_object_type, "", ttcl.train_course_chapter_id)
### Mysql字符串字段判断是否包含某个字符串的方法
- **like**

```mysql
SELECT * FROM 表名 WHERE 字段名 like "%字符%";
```

- **find_in_set()**

```mysql
SELECT * FROM users WHERE find_in_set('字符', 字段名);
```

- **locate(字符,字段名)**

使用locate(字符,字段名)函数，如果包含，返回>0的数，否则返回0 

position in

```mysql
select * from 表名 where locate(字符,字段)
select * from 表名 where position(字符 in 字段);
```

- **INSTR(字段,字符)**

```mysql
select * from 表名 where INSTR(字段,字符)
```

