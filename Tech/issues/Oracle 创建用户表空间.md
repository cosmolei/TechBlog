## Oracle 创建用户表空间


1. 查询用户表空间文件目录 ```
	select * from DBA_DATA_FILES;
```
2. 创建临时表空间
```
	create temporary tablespace SHR_TEMP
	tempfile '/opt/oracle/oradata/cmp/shr_temp.dbf'
	size 50m
	autoextend on
	next 50m maxsize 20480m
	extent management local;
```
3. 创建表空间
```
	create tablespace SHR
	logging
	datafile '/opt/oracle/oradata/cmp/shr.dbf'
	size 50m
	autoextend on
	next 50m maxsize 20480m
	extent management local;
```
4. 创建用户并制定表空间
```
	create user shr identified by shr
	default tablespace shr
	temporary tablespace shr_temp;
```
5. 授权
```
	grant connect,resource,dba to shr;
```

#技术笔记/Oracle#