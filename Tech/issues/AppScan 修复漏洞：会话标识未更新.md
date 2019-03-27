# ## AppScan 修复漏洞：会话标识未更新

“会话标识未更新”是中危漏洞，AppScan会扫描“登录行为”前后的Cookie，其中会对其中的JSESSIONOID（JSP）或者 ASP.NET_SessionId（ASP）进行记录。在登录行为发生后，如果cookie中这个值没有发生变化，则判定为“会话标识未更新”漏洞。
 
解决：
在登录页面上加上一段代码：
request.getSession().invalidate();//清空session
Cookie cookie = request.getCookies()[0](#);//获取cookie
cookie.setMaxAge(0);//让cookie过期

然后用户再输入信息登录时，就会产生一个新的session了。
#技术笔记/安全扫描