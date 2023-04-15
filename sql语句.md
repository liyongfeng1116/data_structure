# sql
#1、查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号和成绩
# 先要查到两个成绩（自连接）用sc的id，查到的用b表示，然后用student与b连接
select a.*, b.score1, b.score2
from 
(
select sc1.sid, sc1.score as score1, sc2.score as score2
from sc as sc1
join sc as sc2
where sc1.sid = sc2.SId and sc1.CId = '01' and sc2.cid = '02' and sc1.score > sc2.score
) as b
join student as a on a.sid = b.sid;

# 2. 查询同时存在" 01 "课程和" 02 "课程的情况
# t1代表01课程的，t2代表02课程的
select * FROM
	(select * from sc where sc.CId = '01') as t1, # 加，号
	(select * from sc where sc.CId = '02') as t2
where t1.sid = t2.sid;

#3. 查询存在" 01 "课程但可能不存在" 02 "课程的情况
# 先查01，然后左外连接02
select * from 
	(select * from sc where sc.CId = '01') as t1
	LEFT JOIN
	(select * from sc where sc.cid = '02') as t2
on t1.sid = t2.sid;
# 4. 查询不存在" 01 "课程但存在" 02 "课程的情况
select * FROM sc
	where sc.sid not in 
	(select sid from sc where sc.cid = '01')
	and sc.CId = '02';


# 5. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩
select a.sname, b.avgscore 
from student as a 
join 
(select sid, avg(score) as avgscore from sc group by sid having avgscore >= 60) as b
on a.sid = b.sid;

# 6.查询在 SC 表存在成绩的学生信息
# 不能用 !=null
select DISTINCT student.*
from student, sc 
where student.sid = sc.sid;

#7.所有成绩小于60分的学生信息
select a.* from student as a 
join 
(select sid, min(score) as minscore from sc group by sid having min(score)< 60) as b
on b.sid = a.sid; 

# 8.查询平均成绩小于60分的学生的学号和平均成绩，考虑没参加考试的情况
select b.sid, avg(IFNULL(b.score,0)) as avgscore FROM  # IFNULL(expr1,expr2)表示第一个不为空，返回第一个，否则返回第二个
(select a.sid, c.score from student as a LEFT JOIN sc as c on a.sid = c.sid) as b
group by b.sid
having avgscore < 60;

# 9. 查询所有学生的学号、姓名、选课数、总成绩
select a.sid, a.sname, count(b.cid), sum(b.score) FROM
student as a left join sc as b on a.sid = b.SId  # 必须用left join 因为有些人没选课
group by a.sid, a.sname; # 非聚合的函数必须都group by

# 10. 查询没学过“张三”老师课的学生的学号、姓名
select a.sid, a.sname from student as a 
where a.sid not in   # 把张三老师教的学生查出，然后查不在这些学生的
(select b.sid from sc as b join course as c on b.cid = c.cid join teacher as d on d.tid = c.tid where d.tname = '张三');

# 查询学过编号为“01”的课程并且也学过编号为“02”的课程的学生的学号、姓名（！）
select a.sid, a.sname from student as a
join 
(select b.* from sc as b join sc as c on b.sid = c.sid where b.cid = '01' and c.cid = '02') as d  # 要用同一个表两次，用自连接
on a.sid = d.sid;
