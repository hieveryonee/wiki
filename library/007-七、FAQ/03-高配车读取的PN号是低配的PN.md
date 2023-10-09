# 高配车读取的PN号是低配的PN

1、首先要有车型配置字

可以进入中控后台，查看[CC配置]菜单下的 VEHICLE_TYPE 的值。

每个车型对应的VEHICLE_TYPE值如下：

```
# G636     176
# G733     172
# G426     226
# FS11-A2  175
# FX11     135
```

通过串口命令可以修改这个配置字，方法如下：   
```
// 其中 176 需要根据以上对应车型替换
// 修改后需要重启车机
# test_psis_car_cfg CFG_CAR_CONFIGURATION 0 176
```

2、对于有高配车，还有另外一个配置字[CC688]来区分高低配

```
1）配置字 CC688（index CC687）必须等于9
2）查看该配置字：QNX串口命令：

  psis_client -r -1 cus_cfg CFG_CAR_CONFIGURATION

  读出来的很长的配置字，拷贝出来，按行排序（空格替换\r\n实现换行），查看687行
```

3、修改该配置字为5R6V车型，命令如下

```
test_psis_car_cfg CFG_CAR_CONFIGURATION 687 9
```

4、修改后不需要重启，直接DSA读取，即为正确的结果
