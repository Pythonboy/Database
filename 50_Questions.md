# 测试表格

#### 1） 学生表

**Student(S#,Sname,Sage,Ssex)**

--S# 学生编号,Sname 学生姓名,Sage 出生年月,Ssex 学生性别

#### 2） 课程表

**Course(C#,Cname,T#)** 

--C# --课程编号,Cname 课程名称,T# 教师编号

#### 3）教师表

**Teacher(T#,Tname）**

 --T# 教师编号,Tname 教师姓名

#### 4）成绩表

**SC(S#,C#,score）**

 --S# 学生编号,C# 课程编号,score 分数



# 创建测试数据

**学生表 Student**

create table Student(S# varchar(10),Sname nvarchar(10),Sage datetime,Ssex nvarchar(10))
insert into Student values('01' , N'赵雷' , '1990-01-01' , N'男')
insert into Student values('02' , N'钱电' , '1990-12-21' , N'男')
insert into Student values('03' , N'孙风' , '1990-05-20' , N'男')
insert into Student values('04' , N'李云' , '1990-08-06' , N'男')
insert into Student values('05' , N'周梅' , '1991-12-01' , N'女')
insert into Student values('06' , N'吴兰' , '1992-03-01' , N'女')
insert into Student values('07' , N'郑竹' , '1989-07-01' , N'女')
insert into Student values('08' , N'王菊' , '1990-01-20' , N'女')



**科目表 Course**
create table Course(C# varchar(10),Cname nvarchar(10),T# varchar(10))
insert into Course values('01' , N'语文' , '02')
insert into Course values('02' , N'数学' , '01')
insert into Course values('03' , N'英语' , '03')



**教师表 Teacher**
create table Teacher(T# varchar(10),Tname nvarchar(10))
insert into Teacher values('01' , N'张三')
insert into Teacher values('02' , N'李四')
insert into Teacher values('03' , N'王五')



**成绩表 SC**
create table SC(S# varchar(10),C# varchar(10),score decimal(18,1))
insert into SC values('01' , '01' , 80)
insert into SC values('01' , '02' , 90)
insert into SC values('01' , '03' , 99)
insert into SC values('02' , '01' , 70)
insert into SC values('02' , '02' , 60)
insert into SC values('02' , '03' , 80)
insert into SC values('03' , '01' , 80)
insert into SC values('03' , '02' , 80)
insert into SC values('03' , '03' , 80)
insert into SC values('04' , '01' , 50)
insert into SC values('04' , '02' , 30)
insert into SC values('04' , '03' , 20)
insert into SC values('05' , '01' , 76)
insert into SC values('05' , '02' , 87)
insert into SC values('06' , '01' , 31)
insert into SC values('06' , '03' , 34)
insert into SC values('07' , '02' , 89)

insert into SC values('07' , '03' , 98)



# 50道练习题

#### 1.查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数

```sql
SELECT a.S#,a.C#,a.score,b.C#,b.score 
FROM SC a INNER JOIN SC b
ON a.S# = b.S#
WHERE a.C# = '01' and b.C# = '02' and a.score > b.score
ORDER BY a.S#;
---------------------------------------------------------------------------------
SELECT a.*,b.C#,b.score 
FROM (SELECT * FROM SC WHERE C# = '01') a
LEFT JOIN (SELECT * FROM SC WHERE C# = '02')b
ON a.S# = b.S#
WHERE a.score > b.score
ORDER BY a.S#;
```



####  1.1 查询同时存在" 01 "课程和" 02 "课程的情况

```sql
SELECT a.*,b.C#,b.score 
FROM (SELECT * FROM SC WHERE C# = '01') a
LEFT JOIN (SELECT * FROM SC WHERE C# = '02')b
ON a.S# = b.S#
WHERE b.C# IS NOT NULL
```



#### 1.2 查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null )

```sql
SELECT a.*,b.C#,b.score 
FROM (SELECT * FROM SC WHERE C# = '01') a
LEFT JOIN (SELECT * FROM SC WHERE C# = '02')b
ON a.S# = b.S#
```



#### 1.3  查询不存在" 01 "课程但存在" 02 "课程的情况

```sql
SELECT * FROM SC WHERE C#='02' and S# NOT IN (SELECT S# FROM SC WHERE C# = '01' )
```



#### 2.查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

```sql
SELECT S#,Sname,AVG(score) average_score
FROM Student s
INNER JOIN SC
ON s.S# = SC.S#
GROUP BY SC.S# HAVING AVG(score) >= 60;
```



#### 3.查询在 SC 表存在成绩的学生信息

```sql
SELECT a.S#,a.Sname,a.Sage,S.Ssex
FROM Student a LEFT JOIN SC 
ON a.S# = SC.S#
WHERE SC.score is NOT NULL
GROUP BY a.S#
(这很差劲)
---------------------------------------------------------------------------------
SELECT * FROM Student WHERE S# IN (SELECT DISTINCT S# FROM SC);
```



#### 4. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )

```sql
SELECT a.S#,a.Sname,b.num,b.summary
FROM Student a
LEFT JOIN (SELECT S#,Count(C#) num, Sum(score) summary FROM SC Group BY S#)b
ON a.S# = b.S#
```



#### 4.1 查有成绩的学生信息

```sql
SELECT a.S#,a.Sname,b.num,b.summary
FROM Student a
RIGHT JOIN (SELECT S#,Count(C#) num, Sum(score) summary FROM SC Group BY S#)b
ON a.S# = b.S#

/*(与上一题的区别在于左右两个表谁主谁次的问题)*/
```



#### 5.查询「李」姓老师的数量 

```sql
SELECT Count(T#) FROM Teacher WHERE Tname LIKE "李%";
```



#### 6. 查询学过「张三」老师授课的同学的信息 

```sql
SELECT * FROM Student
WHERE S# IN(
SELECT DISTINCT S# FROM SC
WHERE C# IN (SELECT C# FROM Course INNER JOIN Teacher ON Course.T# = Teacher.T# WHERE Teacher.Tname = "张三")
)
```



#### 7.查询没有学全所有课程的同学的信息 

```sql
SELECT * FROM Student
WHERE S# NOT IN(
SELECT S# FROM SC GROUP BY S# HAVING Count(C#) = ALL (SELCET Count(C#) FROM Course GROUP BY C#))
```



#### 8.查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息 

```sql
SELECT * FROM Student 
WHERE S# IN(SELECT DISTINCT S# FROM SC WHERE C# IN(SELECT C# FROM SC WHERE S# = '01'))
```



#### 9.查询和" 01 "号的同学学习的课程完全相同的其他同学的信息

```sql
SELECT * FROM Student 
WHERE S# IN(SELECT DISTINCT S# FROM SC WHERE C# IN (SELECT C# FROM SC WHERE S# = '01') and S# != '01' GROUP BY S# HAVING COUNT(C#)>=3)
```



#### 10.查询没学过"张三"老师讲授的任一门课程的学生姓名

```sql
SELECT * FROM Student
WHERE S# NOT IN(
SELECT DISTINCT S# FROM SC
WHERE C# IN (SELECT C# FROM Course 
WHERE T# IN (SELECT T# FROM Teacher WHERE Tname = "张三" ))
)
```



#### 11.查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 

```sql
SELECT s.S#,s.Sname,AVG(b.score)
FROM Student s 
INNER JOIN SC b
ON s.S# = b.S
WHERE S# IN(SELECT DISTINCT S# FROM SC WHERE score < 60 GROUP BY S# HAVING Count(score) >= 2)
GROUP BY S#
```



#### 12.检索" 01 "课程分数小于 60，按分数降序排列的学生信息

```sql
SELECT a.S#,a.Sname,a.Sage,a.Ssex
FROM Student a
INNER JOIN (SELECT S#,score FROM SC WHERE C# = '01' and score < 60) b
ORDER BY b.score DESC
```



#### 13.按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
SELECT S#,
Max(case C# when '01' then score else 0 end)'01',
max(case C# when '02' then score else 0 end)'02',
max(case C# when '03' then score else 0 end)'03',
AVG(score) average 
FROM SC
GROUP BY S# 
ORDER BY average DESC;
```





#### 14.查询各科成绩最高分、最低分和平均分： 以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率,  及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90,要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```sql

select distinct A.C#,Cname,最高分,最低分,平均分,及格率,中等率,优良率,优秀率 from SC A
left join Course on A.C#=Course.C#
left join (select C#,MAX(score)最高分,MIN(score)最低分,AVG(score)平均分 from SC group by C#)B on A.C#=B.C#
left join (select C#,(convert(decimal(5,2),(sum(case when score>=60 then 1 else 0 end)*1.00)/COUNT(*))*100)及格率 from SC group by C#)C on A.C#=C.C#
left join (select C#,(convert(decimal(5,2),(sum(case when score >=70 and score<80 then 1 else 0 end)*1.00)/COUNT(*))*100)中等率 from SC group by C#)D on A.C#=D.C#
left join (select C#,(convert(decimal(5,2),(sum(case when score >=80 and score<90 then 1 else 0 end)*1.00)/COUNT(*))*100)优良率 from SC group by C#)E on A.C#=E.C#
left join (select C#,(convert(decimal(5,2),(sum(case when score >=90 then 1 else 0 end)*1.00)/COUNT(*))*100)优秀率 
from SC group by C#)F on A.C#=F.C#
```



#### 15.按各科成绩进行排序，并显示排名，Score 重复时保留名次空缺

```sql
select *,RANK()over(order by score desc)排名 from SC
```



#### 15.1 按各科成绩进行排序，并显示排名，Score 重复时合并名次

```sql
select *,DENSE_RANK()over(order by score desc)排名 from SC
```



#### 16. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺

```sql
SELECT a.S#,a.Score,RANK()over(order by a.Score desc)排名
FROM (SELECT S#,Sum(score) Score FROM SC GROUP BY S#)
```



#### 16.1 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺

```sql
SELECT a.S#,a.Score,DENSE_RANK()over(order by a.Score desc)排名
FROM (SELECT S#,Sum(score) Score FROM SC GROUP BY S#)
```



#### 17.统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比 

```sql
select distinct A.C#,B.Cname,C.[100-85],C.所占百分比,D.[85-70],D.所占百分比,E.[70-60],E.所占百分比,F.[60-0],F.所占百分比
from SC A 
left join Course B ON A.C#=B.C#
left join (select C#,sum(case when score>85 and score<=100 then 1 else null end)[100-85],
convert(decimal(5,2),(sum(case when score>85 and score<100 then 1 else null end))*1.00/COUNT(*))*100 所占百分比 from SC group by C#)C on A.C#=C.C#
left join (select C#,sum(case when score>70 and score<=85 then 1 else null end)[85-70],
convert(decimal(5,2),(sum(case when score>70 and score<=85 then 1 else null end))*1.00/COUNT(*))*100 所占百分比 from SC group by C#)D on A.C#=D.C#
left join (select C#,sum(case when score>60 and score<=70 then 1 else null end)[70-60],
convert(decimal(5,2),(sum(case when score>60 and score<=70 then 1 else null end))*1.00/COUNT(*))*100 所占百分比 from SC group by C#)E on A.C#=E.C#
left join (select C#,sum(case when score>0 and score<=60 then 1 else null end)[60-0],
convert(decimal(5,2),(sum(case when score>0 and score<=60 then 1 else null end))*1.00/COUNT(*))*100 所占百分比 from SC group by C#)F on A.C#=F.C#

```



#### 18.查询各科成绩前三名的记录

```sql
select * from(select *,rank()over (partition by C# order by score desc)A from SC)B where B.A<=3
```



#### 19. 查询每门课程被选修的学生数

```sql
SELECT C#,Count(S#) FROM SC GROUP BY C#
```



#### 20. 查询出只选修两门课程的学生学号和姓名

```sql
SELECT S#,Sname FROM Student 
WHERE S# IN(SELECT S# FROM SC GROUP BY S# HAVING Count(C#) = 2)
```



#### 21 . 查询男生、女生人数

```sql
SELECT Ssex,Count(Ssex) FROM Student GROUP BY Ssex;
```



#### 22.查询名字中含有「风」字的学生信息

```sql
SELECT * FROM Student WHERE Sname LIKE "%风%"；
```



#### 23.查询同名同性学生名单，并统计同名人数

```sql
/*SELECT S#,Count(S#) FROM 
Student a
INNER JOIN Student b
ON a.Sname = b.Sname
WHERE a.S# != b.S# and a.Ssex = b.Ssex
GROUP BY S#*/
select A.*,B.同名人数 from Student A
left join (select Sname,Ssex,COUNT(*)同名人数 from Student group by Sname,Ssex)B 
on A.Sname=B.Sname and A.Ssex=B.Ssex
where B.同名人数>1
```



#### 24. 查询 1990 年出生的学生名单

```sql
SELECT * FROM Student WHERE Sage BETWEEN "1990-1-1"and "1990-12-31";

SELECT * FROM Student WHERE YEAR(Sage) = 1990
```



#### 25.查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

```sql
SELECT C#,AVG(score) average FROM SC GROUP BY C# ORDER BY average DESC, C# ASC;
```



#### 26. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩

```sql
 SELECT a.S#,a.Sname,b.average
 FROM Student a 
 INNER JOIN (SELECT S#,AVG(score) average FROM SC GROUP BY S# HAVING average>=85)b
 ON a.S# = b.S#;
```



#### 27. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 

```sql
SELECT a.Sname,b.Score 
FROM Student a
INNER JOIN (SELECT SC.S# S#,SC.score Score FROM SC INNER JOIN Course ON SC.C# = Course.C# WHERE Course.Cname = "数学" and SC.score < 60 )b
ON a.S# = b.S#

SELECT a.Sname,b.score 
FROM Student a
INNER JOIN(SELECT * FROM SC WHERE C# IN(SELECT DISTINCT C# FROM Course WHERE Cname = "数学") and score < 60 )b
ON a.S# = b.S#

```



#### 28.查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）

```sql
select A.S#,B.C#,B.score from Student A left join SC B on A.S#=B.S#
```



#### 29.查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数

```sql
SELECT a.Sname,c.Cname,b.score 
FROM Student a
INNER JOIN (SELECT * FROM SC WHERE score > 70)b ON a.S# = b.S#
INNER JOIN Course c ON b.C# = c.C#;
```



####  30. 查询不及格的课程

```sql
SELECT * FROM SC WHERE score < 60;
```



#### 31.查询课程编号为01且课程成绩在80分以上的学生的学号和姓名

```sql
SELECT S#,Sname FROM Student
WHERE S# IN (SELECT S# FROM SC WHERE C# = '01' and score > 80);
```



#### 32.求每门课程的学生人数

```sql
SELECT c.Cname,d.Num
FROM Course c
LEFT JOIN (SELECT C#,Count(S#) Num FROM SC GROUP BY C#)d
ON c.C# = d.C#
```



#### 33.成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

```sql
select top 1* from SC 
where C#=(select C# from Course where T#=(select T# from Teacher where Tname='张三')) 
order by score desc

select * from SC 
where C#=(select C# from Course where T#=(select T# from Teacher where Tname='张三')) 
order by score desc
LIMIT 1 OFFSET 0;
```



#### 34.成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

```sql

select *from(select *,DENSE_RANK()over (order by score desc)A 
from SC 
where C#=(select C# from Course where T#=(select T# from Teacher where Tname='张三')))B
where B.A=1
```



#### 35.查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

```sql
SELECT * FROM SC
WHERE S# IN(SELECT DISTINCT S# FROM SC GROUP BY S# HAVING MAX(score) = MIN(score) and Count(score) > 1)
```



#### 36.查询每门功成绩最好的前两名

```
select * from
(select *,ROW_NUMBER()over(partition by C# order by score desc)A from SC)B
where B.A<3
```



#### 37.统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```sql
SELECT C#,Count(S#) Num FROM SC
GROUP BY C# HAVING Count(S#) > 5 
ORDER　BY Num DESC,C# ASC;
```



#### 38.检索至少选修两门课程的学生学号 

```sql
SELECT S# FROM SC
GROUP BY S# HAVING Count(C#) > 1
```



#### 39.查询选修了全部课程的学生信息

```sql
SELECT * FROM Student
WHERE S# IN(SELECT DISTINCT S# FROM SC GROUP BY S# HAVING Count(C#) = (SELECT Count(0) FROM Course))
```



#### 40.查询各学生的年龄，只按年份来算

```sql
select S#,datediff(yy,Sage,GETDATE())年龄 from Student
```



#### 41.查询本周过生日的学生

```sql
select *,(case when datename(wk,convert(datetime,(convert(varchar(10),year(GETDATE()))+substring(convert(varchar(10),Sage,112),5,8))))=DATENAME(WK,GETDATE()) 
then 1 else 0 end)生日提醒
from Student
```



#### 42.查询本月过生日的学生

```sql
select *,(case when month(convert(datetime,(convert(varchar(10),year(GETDATE()))+substring(convert(varchar(10),Sage,112),5,8))))=month(GETDATE())
then 1 else 0 end)生日提醒
from Student
```



#### 43.查询下周过生日的学生

```sql
select *,(case when datename(wk,convert(datetime,(convert(varchar(10),year(GETDATE()))+
substring(convert(varchar(10),Sage,112),5,8))))=DATENAME(WK,GETDATE())+1 
then 1 else 0 end)生日提醒
from Student
```



#### 44.查询下月过生日的学生

```sql
select *,(case when month(convert(datetime,(convert(varchar(10),year(GETDATE()))+substring(convert(varchar(10),Sage,112),5,8))))=month(GETDATE())+1
then 1 else 0 end)生日提醒
from Student
```

