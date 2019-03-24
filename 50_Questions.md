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





















