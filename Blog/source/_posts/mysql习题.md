

```mysql
##查询“01”课程比“02课程”成绩高的学生信息 及 课程分数
##自连接
SELECT
	a.s_id,
	a.s_score s01,
	b.s_score s02 
FROM
	score a,
	score b 
WHERE
	a.c_id = '01' 
	AND b.c_id = '02' 
	AND a.s_id = b.s_id 
	AND a.s_score > b.s_score
##把长型数据变成宽型数据
select 
s.*,t.s01,t.s02
from 
(select
a.s_id,
max(case when a.c_id = '01' then a.s_score end) s01,
max(case when a.c_id = '02' then a.s_score end) s02
from score a
	group by a.s_id) t,student s
where t.s01>t.s02
and t.s_id=s.s_id
##查询01课程成绩比02课程低的学生信息及课程分数
##与第一题类似
SELECT
	s.*,
	a.s_score s01,
	b.s_score s02 
FROM
	score a,
	score b,
	student s 
WHERE
	a.s_id = b.s_id 
	AND a.c_id = '01' 
	AND b.c_id = '02' 
	AND a.s_score < b.s_score 
	AND a.s_id = b.s_id 
	AND a.s_id = s.s_id
	
	
	
	###查询成绩总和，选课数量
select 
b.s_id,b.s_name,count(a.c_id) cnt_s,
sum(a.s_score) sum_s
from 
score a
right JOIN
student b
on
a.s_id = b.s_id
GROUP BY
b.s_id,b.s_name


select
s_id,s_name,
(SELECT COUNT(1) from score WHERE score.s_id = student.s_id) number,
(SELECT SUM(s_score) from score WHERE score.s_id = student.s_id) sum
from student

###查询李姓老师
select count(t_name)
from teacher where t_name like '李%';
###查询学过张三老师授课的同学的信息

select s1.*,s.s_id from score s,student s1 where s.c_id=
(select c.c_id from teacher t inner join course c on t.t_id=c.t_id and t.t_name='张三') and s1.s_id=s.s_id

select s.*
from student s,course c,score s1,teacher t
where t.t_id = c.t_id
and  c.t_id =s1.s_id
and  s1.s_id = s.s_id
and  t.t_name='张三'
###查询mei学过张三老师授课的同学的信息 直接剔除掉
select s.*
from 
student s
where s.s_id not in (select s1.s_id from score s,student s1 where s.c_id=
(select c.c_id from teacher t inner join course c on t.t_id=c.t_id and t.t_name='张三') and s1.s_id=s.s_id)
###查询学过编号001和编号002的课程的同学的信息
select 
c.*
from 
	score a,score b,student c
where a.c_id='1'
and b.c_id='2'
and a.s_id=b.s_id
and c.s_id=a.s_id
###查询学过编号001和没学过编号002的课程的同学的信息
select s.* from (
select 
	s_id,
	max(case WHEN c_id='1' then s_score end) s01,
	max(case WHEN c_id='2' then s_score end) s02
from 
score a
GROUP BY
a.s_id
) t,student s
where 
t.s_id=s.s_id
and t.s01 is not null 
and t.s02 is null

###查询没有学全所有课程的同学的信息
###先查所有同学学了多少课程
select s.s_id,s1.* from
(
select
a.s_id,
COUNT(b.c_id) cnt
from 
student a
left join score b
on a.s_id = b.s_id
GROUP BY
a.s_id
) s,student s1
where s.cnt = 3
and s1.s_id=s.s_id
###查询至少有一门课与学号为1 的同学所学相同的同学信息
select
distinct a.*
from 
student a
left join score b
on a.s_id = b.s_id
where b.c_id in (SELECT c_id from score where s_id='1')
```

