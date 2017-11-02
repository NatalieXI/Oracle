# Oracle
oracle常见操作记录
# ORACLE查询死锁的表、解锁
```
select sess.sid,
   sess.serial#,
   lo.oracle_username,
   lo.os_user_name,
   ao.object_name,
   lo.locked_mode
   from v$locked_object lo,
   dba_objects ao,
   v$session sess
where ao.object_id = lo.object_id and lo.session_id = sess.sid
order by ao.object_name ;
alter system kill session '1157,13643'; 

字段说明：
Username：死锁语句所用的数据库用户；
SID: session identifier， session 标示符，session 是通信双方从开始通信到通信结束期间的一个上下文。
SERIAL#: sid 会重用，但是同一个sid被重用时，serial#会增加，不会重复。
Lockwait：可以通过这个字段查询出当前正在等待的锁的相关信息。
Status：用来判断session状态。Active：正执行SQL语句。Inactive：等待操作。Killed：被标注为删除。
Machine： 死锁语句所在的机器。
Program： 产生死锁的语句主要来自哪个应用程序。
```
# 查询导致锁表的语句
```
SELECT sql_text
FROM v$sql
WHERE hash_value IN
  (SELECT sql_hash_value
  FROM v$session
  WHERE sid IN
    (SELECT session_id FROM v$locked_object
    )
  );  
```
