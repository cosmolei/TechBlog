#  Oracle IMPDP/EXPDP 用法示例

#### expdp 排除一些表导出
expdp只能用于服务器端导出, 注意转义字符。
	expdp xbjszy/xbjszy@orcl directory=BACKUP_DIR dumpfile=xbjszydmp.dmp logfile=xbjszylog.log EXCLUDE=TABLE:\”IN\(\’XT_ALARM_GJZX_GJHISTORY\’,\’XT_ZYJK_DAY_SERVERS_HISTORY\’,\’FLEX_SOFTWARE_DAILY\’,\’XT_ZYJK_DAY_NODE_HISTORY\’,\’XT_ZYGL_CLUSTER_NODE_HISTORY\’,\’XT_HISTORY_LUN\’,\’COLLECTION_LOG\’,\’MGMT_METRICS_RAW\’,\’XT_ZYGL_LUN_FILEINFO\’,\’XT_ZYJK_DAY_CLUSTER_HISTORY\’,\’XT_LOGIN_TIME\’,\’FLEX_USED\’,\’LDAP_LOG_TIME\’\)\”

#### impdp 导入数据

	impdp xbjszy/xbjszy@orcl DIRECTORY=DATA_PUMP_DIR DUMPFILE=xbjszydmp.dmp SCHEMAS=xbjszy

其中，DIRECTORY 需要sql语句创建，例如：
	select * from dba_directories;   //查看当前创建的directories
	create directory BACKUP_DIR as ‘/home/oracle/backup’; //创建一个directory
	grant read,write on directory BACKUP_DIR to xbjszy; // 给用户赋予读写该目录的权限


#技术笔记/Oracle