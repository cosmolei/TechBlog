# 关于PCS监控中tomcat监控配置jmxremote只有本机可以用jconsole查看到，远程这个ip报错无法正常连接的解决办法
#工作/问题清单

增加下述行中第一行指定被监控机器的IP就可以了（花费了半天时间才终于解决这个问题）
```
-Djava.rmi.server.hostname=192.168.1.122
-Dcom.sun.management.jmxremote
-Dcom.sun.management.jmxremote.port=911
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false
```

