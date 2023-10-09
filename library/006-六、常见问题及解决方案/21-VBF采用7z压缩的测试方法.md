# VBF采用7z压缩的测试方法

测试目的：
测试合入诊断仪的优化、VBF压缩采用7z的优化、以及DHU中支持7z VBF解压的优化的场景下，总体的升级耗时情况。


前提条件：
>1、由于涉及到升级方式的兼容性，因此无法直接将代码合入代码仓库  
2、DHU需要先推包，在验证


操作步骤：  

- **第一步，升级前先在DHU上增加7z工具**
  ```
  # 通过 adb方式将 7za 上传到DHU后台：/apps/update/bin 目录
  # adb的推包方式参考[01-推包指导书]
  ```
  QNX版本的7z工具：- [7za](assets/bin/7za)

- **第二步，替换 UpdateService,让DHU升级过程能正确解压7z VBF**  

  ```
  # 推包方式参考[01-推包指导书]
  # 重启车机
  ```
  待替换的 UpdateService：- [UpdateService](assets/bin/UpdateService)

- **第三步，用测试VBF测试**
  ```
  # 7z版本的VBF 路径: https://bcebos.cloud.geely.com/v1/zuocang-gerrit-autolink/autolink/VBF_7Z_TEST/
  ```


以上就是完成了VBF的7z压缩和解压缩，请继续采用 诊断仪优化的脚本条件下，端到端测试总耗时情况。
