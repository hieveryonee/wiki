# SOC日志规范

## 1 日志打印规则

```
时间 -- 进程ID -- xx -- 线程ID -- 日志级别 -- 进程名称 -- 模块名称 -- 函数:行号 -- 自己打印内容

如：
1970-01-01 00:00:04.81         CMW_Cluster.516165..1   173     1 I
[APA][APAServer][SendPDCMuteState:2570]  sendvalue is 0
```


## 2 各个模块如何打印日志

```
1、替换头文件 #include "Log.h" 为 #include "ClusterLog.h"
2、使用 clusterLog.h中定义好的打印宏替换原始的APP_LOG_INFO之类
3、删除自己代码日志中的模块、函数

参考：DiagServer.cpp
http://gerrit.auto-link.com.cn:8086/c/cluster/SOC/middleware/+/216910
```

## 3 Soc中有那些进程和线程

3.1 进程

```
cluster-app
cluster-anim
welcomeshow-anim
ivi-anim
psd-anim
system-monitor
app-launcher
apa-app
dms-app
hvac-app
cluster-early-app
```

3.1 线程

以cluster-app为例

![img](assets/heiping/thread_info.png)



总结：

1、线程：MainProcess1

```
各个server公用改线程，比如：SettingServer、StateMgrServer、KeyServer、TCServer、WarningServe....
```

2、类似 svc:/HUDServer 线程

```
同一个Server中 所有的Proxy 对应公用一个线程，比如共用 svc:/HUDServer 线程
大概有20个server，所以大概有20个线程
```

3、TimerThread线程+TimerQueueThread线程

由于timer是采用回调函数的方式，因此各个模块的timer回调函数均在TimerQueueThread线程中执行

```
TimerThread线程负责计时；
TimerQueueThread线程负责执行超时的定时任务
```

总结：一个模块（server）的代码，可能主要在三个线程中执行：

```
* 所有server共用的主线程MainProcess1
* 每个server拥有的一个proxy 线程（用户数据接受）
* 定时任务的TimerQueueThread线程
```



要注意自己代码都是在那个线程中执行



## 4 如果在日志中过滤进程+线程

```
notepad++ 中选择正则表达式如下：

(680010)(\.\.\d+)*\s+\d+\s+(7)

其中 680010=进程ID  7=线程ID
```


## 5 那些模块是每个进程均有一份的？

settingProxy

timer
