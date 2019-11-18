# mysql常用知识点
## 修改表结构
### 在某个字段之后添加表字段  默认值 注释
ALTER TABLE goods_goods ADD isTrainCourse INT ( 1 ) DEFAULT 0 COMMENT '是否是实训课程 (0: 不是, 1: 是)' AFTER isExam;
## 常用mysql函数
### CONCAT_WS(separator,str1,str2,…)函数
SELECT CONCAT_WS(',','First name',NULL,'Last Name');返回结果为
First name,Last Name
### IF(expr1,expr2,expr3)函数
IF("document"=ttcl.train_lesson_object_type, "", ttcl.train_course_chapter_id)
## 
## 
## 
