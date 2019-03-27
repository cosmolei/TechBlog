#  Linux 使程序后台运行的方法

1. 在终端输入命令：
	# ./pso > pso.file 2>&1 & 
解释：将pso直接放在后台运行，并把终端输出存放在当前目录下的pso.file文件中。当客户端关机后重新登陆服务器后，直接查看pso.file文件就可看执行结果（命
令：#cat pso.file ）。
2. 在终端输入命令：
	# nohup  ./pso > pso.file 2>&1 &
解释：nohup就是不挂起的意思，将pso直接放在后台运行，并把终端输出存放在当前
目录下的pso.file文件中。当客户端关机后重新登陆服务器后，直接查看pso.file
文件就可看执行结果（命令：#cat pso.file ）。
#技术笔记/Linux