# SQL统计不同分数段的学生人数

给定一张数据库表 SS如下，要求统计不同分数段的学生人数，例如70-79,80-89.

----------------------------

student_id	score
01	75
02	77
03	82

---------

方案一：

对每个分数段的学生人数分别做查询统计，最后组合到一起。

```
SELECT COUNT(student_id) FROM SS WHERE score>=inputScore and score<(inputScore+10);
```


方案二：

对分数做10的整除，得到了0,1,2,3...10等分组。

注意sql中 int整除后有小数，要用FLOOR做向下取整。

```
SELECT COUNT(student_id) FROM SS GROUP BY FLOOR(score/10);
```



