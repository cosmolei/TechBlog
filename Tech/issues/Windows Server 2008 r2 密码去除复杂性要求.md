#  Windows Server 2008 r2 密码去除复杂性要求
关于“密码不满足密码策略的要求，检查最下密码长度、密码复杂性和密码历史的要求”的解决办法（Windows 2003 &Windows 2008）
由于域的规约而导致的问题，问题在于密码设定不符合策略组的规约。此时需要到域策略中设置响应选项来降低密码的复杂度。（默认的复杂度需要至少7字符，且包含多个字母和数字） 

Windows Server 2003解决办法是：
选择**开始\>程序\>管理工具\>域安全策略\>帐户策略\>密码策略**。密码必须符合复杂性要求：由“已启用”改为“已禁用”；密码长度最小值：由“7个字符”改为“0个字符”  使此策略修改生效有如下方法：
1. 等待系统自动刷新组策略，约5分钟-15分钟；
2. 重启域控制器（若是修改的用户策略，注销即可）；
3. 使用gpupdate命令。 
	仅刷新计算机策略：gpupdate/target:computer
	仅刷新用户策略：gpupdate/target:user 
	二者都刷新：gpupdate

Windows Server 2008不一样的是, 管理员不能从本地策略组中将其密码策略修改, 而需要从GPM(Group Policy Management)”组策略管理器”中进行修改。步骤如下:
Windows Server 2008中, 打开GPM(Group Policy Management)方法: 依次选择**start(开始)-\>Administrator Tools(管理者工具)\>Group Policy Management(组策略管理器)-\>Run as administrator/Open(使用管理员权限打开)**。

开启GPM之后, 依次展开树状节点:
**Forest: YOUR DOMAIN NAME(你的域名)-\>domain(域)-\>YOUR DOMAIN NAME(你的域名)-\>Group Policy Object(组策略对象)**。右键点击Default Domain Policy(默认域策略), 选择Edit(编辑)。

以上操作后将打开Group Policy Management Editor(组策略管理器)对话框, 依次展开树状节点: **Computer Configuration(计算机配置)-\>Policy(策略)-\>Windows Settings(Windows设置)-\>Security Settings(安全设置)-\>Account Policy(账户策略)-\>Password Policy(密码策略)**。可以看到关于密码的策略设定, 只要将倒数第二项：Password must meet complexity requirments(密码复杂度)项目设成disable即可。其余的策略因情况而设定。 

最后选择start-\>run(运行), 输入gpupdate强制更新组策略设置。等待命令行结束之后即可完成。
#技术笔记/AD#
