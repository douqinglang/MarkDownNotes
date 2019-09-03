# mysql常用知识点
## 修改表结构
### 在某个字段之后添加表字段  默认值 注释
ALTER TABLE goods_goods ADD isTrainCourse INT ( 1 ) DEFAULT 0 COMMENT '是否是实训课程 (0: 不是, 1: 是)' AFTER isExam;