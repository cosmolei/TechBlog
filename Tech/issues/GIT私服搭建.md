#GIT私服搭建

到某目录，假设是～，创建一个裸库：
```bash
cd ~
git init --bare sample.git
```

客户机获取该库：
```bash
git clone user@host:/home/user/sample.git
```
