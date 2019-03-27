# ## nis用户限制访问ftp

###ftp白名单
编辑`/etc/vsftpd/vsftpd.conf`
增加或修改如下行：
userlist_enable=YES   ##启用user_list文件控制，启用后deny才起作用
userlist_deny=NO  ##NO:user_list为白名单，yes为黑名单

###sftp设置
只要修改ssh的白名单就可以限制nis用户访问
编辑`/etc/ssh/sshd_config`
增加或修改如下行：
AllowUsers root
重启sshd服务
service sshd restart

#工作/备忘