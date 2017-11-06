# 对比数据库表结构,脚本
```
DECLARE
    dd VARCHAR(200);
    TYPE cur IS REF CURSOR;
    tableName_cursor cur;
    v_sql   VARCHAR2(1000);
    v_bj    NUMBER:=0;
    v_count NUMBER:=0;
  BEGIN
    SELECT COUNT(TABLE_NAME) INTO v_count FROM (SELECT DISTINCT TABLE_NAME FROM DBA_TAB_COLUMNS WHERE OWNER='DB_DKDZ_DEV' AND TABLE_NAME NOT LIKE 'B%');
    v_sql :='select DISTINCT TABLE_NAME from DBA_TAB_COLUMNS where OWNER=''DB_DKDZ_DEV'' and TABLE_NAME not like ''B%''';
    OPEN tableName_cursor FOR v_sql;
    WHILE v_bj <v_count LOOP
    FETCH tableName_cursor INTO dd;
    INSERT INTO TABLE_DIFRENT
      (
      TABLE_NAME,
      COLUMN_NAME,
      DATA_LENGTH
      )
    (
    SELECT TABLE_NAME,COLUMN_NAME,DATA_LENGTH
    FROM DBA_TAB_COLUMNS
    WHERE OWNER    ='DB_DKDZ_DEV'
    AND TABLE_NAME = dd
    AND column_name NOT IN
      (SELECT s1.column_name FROM
        (SELECT *
        FROM DBA_TAB_COLUMNS
        WHERE OWNER   ='DB_DKDZ_DEV'
        AND TABLE_NAME=
          dd
        ) s1
      JOIN
        (SELECT *
        FROM DBA_TAB_COLUMNS
        WHERE OWNER   ='DB_DKDZ_TEST'
        AND TABLE_NAME=dd
        ) s2
      ON s1.column_name =s2.column_name
      AND s1.data_length=s2.data_length
      ));
    v_bj := v_bj+1;
  END LOOP;
END;

```
