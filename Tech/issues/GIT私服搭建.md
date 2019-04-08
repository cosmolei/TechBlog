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

PS:git init 与git init --bare 的区别是，git init --bare是初始化一个裸仓库，没有工作区，只会记录git提交的历史信息，没办法进行版本回退或者切换分支。可以作为静态代码仓库。