# ### centos 7 安装postgresql

#### 按照官方步骤安装数据库

#### 修改两个文件
文件位于`/var/lib/pgsql/9.6/data/`目录下
修改`postgresql.conf`
启用以下两行
```bash
listen_addresses = '*'
port = 5432
```
修改`pg_hba.conf`
增加如下行
```bash
host     all             all             0.0.0.0/0               trust
```

重启服务
```bash
systemctl restart postgresql-9.6
```

防火墙设置不禁止
```bash
firewall-cmd --zone=public --add-port=5432/tcp --permanent
```
重启防火墙服务
```bash
systemctl restart firewalld.service
```

#工作/备忘