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
