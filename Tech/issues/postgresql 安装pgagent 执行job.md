# ## postgresql 安装pgagent 执行job

1. 安装pgagent
```bash
[root@VM_0_9_centos ~]# yum install pgagent_96.x86_64
```
2. postgresql 数据库集成 pgagent
```bash
[root@VM_0_9_centos ~]# su - postgres
-bash-4.2$ psql

postgres=# CREATE EXTENSION pgagent;
postgres=# \q
```
3. 开启pgAgent守护程序
```bash
[root@VM_0_9_centos ~]# pgagent_96 hostaddr=127.0.0.1 dbname=postgres user=postgres password=adagio314159
```
4. 客户机安装pgAdmin
5. 创建job（作业：包含计划(schedule)和多个步骤(step)）
- 链接服务器

![](DraggedImage.png)
- 创建作业
![](postgresql%20%E5%AE%89%E8%A3%85pgagent%20%E6%89%A7%E8%A1%8Cjob/DraggedImage.627d992f6cbf469f9016644a7914d049.png)
![](DraggedImage-1.png)
- 创建计划
![](postgresql%20%E5%AE%89%E8%A3%85pgagent%20%E6%89%A7%E8%A1%8Cjob/DraggedImage.95efe2d431b244fda7434cedabc087cb.png)
![](DraggedImage-2.png)
- 创建步骤
![](postgresql%20%E5%AE%89%E8%A3%85pgagent%20%E6%89%A7%E8%A1%8Cjob/DraggedImage.295cbffe5f7a4ffd900ec7786bf30c07.png)
![](DraggedImage-3.png)

#工作/备忘