# 如何解析core文件

>1. [ADB方式推包](#ADB方式推包 "ADB方式推包")
1. [UpdateService 替换指导书](#UpdateService 替换指导书 "UpdateService 替换指导书")


前提条件：
>1、本地电脑安装ADB工具  
2、ADB连接正常、车机启动  
3、车机版本和linux上编译环境的代码差异要小


## 第一部分：重新编译对应的问题库，并替换到车机上，复现问题，产生core文件

默认情况，当前编译是没有导出符号表的，因此需要增加编译参数，重新复现产生core。  

以下案例以 libMiddleware.so 为例

操作步骤：  

- **第一步，增加编译参数-g，重新编译so**  
  在 /Data/maxuefeng/workspace/GLY_G426_SOP/workspace/Middleware/CMakeLists.txt 中增加如下行   
  ```
  # add_compile_options(-g)
  ```

- **第二步，将新生成的 libMiddleware.so 替换到车机上**  

  替换方法，参见《推包指导书》

- **第三步，复现问题，并产生对应的core文件**
  core 文件目录。默认在QNX /var/log 目录下生成core文件  
  ```
  # ls /var/log/
  cluster-app.core.gz
  ```  

  下载该文件，解压得到： cluster-app.core


## 第二部分：在linux上用gdb解析core文件  

1、由于车机上没有gdb，因此需要将依赖的相关库从车机上拷贝到linux上，在linux上用gdb解析  
2、由于需要交叉编译，因此需要用linux环境上的QNX版本的gdb

操作步骤：  

- **第一步，拷贝需要的依赖SO**  
  相关的依赖库，可以先拷贝到共享 /shared 目录，然后统一复制  
  以下这些库是动态变化的，根据自己的bin文件，拷贝对应的依赖  

  ```
  // 目录结构如下，正常需要全部拷贝车机到linux上，但是太大了，无法全部拷贝  
  --shared  
    --lib             # 业务lib 例如 /apps/cluster/FX11_HEV/lib/
    --commonlib       # 业务common lib 例如 /apps/cluster/common/lib/
    --lib64           # boash 和 系统 lib 例如 /lib64
    --corefile        # 放core文件

  // 创建lib
  # mkdir shared/lib
  # mkdir shared/commonlib
  # mkdir shared/lib64
  # mkdir shared/corefile

  // 拷贝：lib 库文件
  # cp -rf /apps/cluster/FX11_HEV/lib/libMidProxy.so  /shared/lib/
  # cp -rf /apps/cluster/FX11_HEV/lib/libHvacProxy.so  /shared/lib/
  # cp -rf /apps/cluster/FX11_HEV/lib/libDmsProxy.so  /shared/lib/
  # cp -rf /apps/cluster/FX11_HEV/lib/libAnimProxy.so  /shared/lib/

  // 拷贝：公共 lib 库文件
  # cp -rf /apps/cluster/common/lib/libSystemIPC.so  /shared/commonlib/

  // 拷贝：博世 lib 库文件
  # cp -rf /lib64/libcommon_base.so  /shared/lib64/
  # cp -rf /lib64/libprotobuf.so.3.19.4.0  /shared/lib64/
  # cp -rf /lib64/libprotobuf-lite.so.3.19.4.0  /shared/lib64/
  # cp -rf /lib64/libc.so.4  /shared/lib64/libc.so.4

  // 拷贝：core 文件
  # cp -rf /var/log/cluster-app.core.gz  /shared/corefile/

  ```

- **第二步，ADB的方式，下载到本地**  
  ```
  // adb pull
  # adb pull /data/vendor/nfs/shared/lib  C:\Users\maxuefeng\Desktop\dumpcore\
  # adb pull /data/vendor/nfs/shared/commonlib  C:\Users\maxuefeng\Desktop\dumpcore\
  # adb pull /data/vendor/nfs/shared/lib64  C:\Users\maxuefeng\Desktop\dumpcore\
  # adb pull /data/vendor/nfs/shared/corefile  C:\Users\maxuefeng\Desktop\dumpcore\

  ```  

- **第三步，本地依赖的库上传到linux服务器**    
  假设当前代码编译后目录： /Data/maxuefeng/workspace/GLY_G426_SOP/project/projroot/arm-QNX/usr/  

  // 目录结构如下  
  ![](assets/faq/core_1.png)

  ```
  // linux 上执行如下命令
  # source /opt/qnx/qnx700_hqx1.2.1/qnxsdp-env.sh
  #
  // 导出lib库路径
  # export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/Data/maxuefeng/workspace/GLY_G426_SOP/project/projroot/arm-QNX/usr/lib/:/Data/maxuefeng/workspace/GLY_G426_SOP/project/projroot/arm-QNX/usr/commonlib/:/Data/maxuefeng/workspace/GLY_G426_SOP/project/projroot/arm-QNX/usr/lib64/
  #
  // 解析core 文件
  # /opt/qnx/qnx700_hqx1.2.1/host/linux/x86_64/usr/bin/ntoaarch64-gdb ./bin/cluster-app ./corefile/cluster-app.core
  #  
  ```  

  // gdb中输入bt指令，参看解析结果：

  ![](assets/faq/core_2.png)
