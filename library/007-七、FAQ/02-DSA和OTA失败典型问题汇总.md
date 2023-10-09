# DSA/OTA失败典型问题汇总

<table>
    <tr>
      <th>问题标题</th>
  		<th>问题描述</th>
  		<th>原因分析</th>
  		<th>解决方案</th>
    </tr>
    <!--模块1--->
    <tr>
      <td> K1K2刷新失败 </td>
  		<td> GEELY-35838 刷写K1K2失败，获取联网证书失败 </td>
  		<td> 诊断仪没有从TC远端系统获取到对应K1K2 VBF包，或者获取的K1K2包有问题，导致K1K2无法刷入。表现为：电检脚本没有执行K1K2相关命令，以及DSA工具直接刷写失败。 </td>
  		<td> 重试，或者 从诊断仪上手动触发 重新从TRC系统上获取K1K2 </td>  
    </tr>

    <!--模块1--->
    <tr>
      <td> TO版本号不一样 </td>
      <td> 读取的PN号为0 </td>
      <td> 不清楚每个PN号的写入方法和获取的规则 </td>
      <td>
      1、请参考<DU HWSD零件号修改方法> 和 <TestOrder介绍> 章节了解每个PN号的写入和读取命令 <br>
      2、如果是很老的版本（大概22年的版本），升级到最新版本，会导致升级后DU零件号为0, 此时需要重新DID写入一次。后续就不会再出现该问题（新老方案变更导致） <br>
      3、如果读出来的值和TestOrder中不一致，请查看当前配置字中的车型配置。 <br>
      4、如果DHU上车型配置字没有配置，也会导致PN为0
      </td>  
    </tr>


    <!--模块1--->
    <tr>
      <td> SWDL工位报DHU完整性校验失败 </td>
      <td> SWDL工位报DHU''完整性校验失败''</td>
      <td>
      票：GEELY-36627、GEELY-38002 <br>
      原因接口：int32_t Security_StartApp(); 调用失败 </td>
      <td> 非必现  int32_t Security_StartApp();这个接口模块当时有问题。<br>
      这个接口直接调用的高通接口，异常的那次启动高通自己的ta模块也加载失败，已经提case给高通"<br>
      博世-张虎 </td>  
    </tr>

    <!--模块1--->
    <tr>
      <td> 电检通讯丢失 </td>
      <td> 所有和1202的诊断命令没反应，通讯丢失</td>
      <td>
      票：GEELY-37260/GEELY-36791/GEELY-38468 <br>
      doip connect server failure : state = 257, errno = 257, errstr = Socket is not connected</td>
      <td> 非必现。<br>
      没有找到根因。<br>
      UDS协议中 DHU 发起和BGM连接，5毫秒重连一次，一直重试，并且每 20次（100ms）调用回调uds_service_connect_abnormal_handler一次。<br>
      解决方法：重做电检</td>
    </tr>


    <!--模块1--->
    <tr>
      <td> 日志丢失 </td>
      <td> normal日志中发现cycle序号缺失，且没有recovery目录</td>
      <td>
      票：GEELY-38405
      </td>
      <td></td>
    </tr>


    <!--模块1--->
    <tr>
      <td> OTA安装失败 </td>
      <td> 报错原因：UPDATEERROR error code : 0x06 : vbf format incorrect </td>
      <td>
      票：GEELY-40741 OTA升级vbf包解析报错<br>
      原因参见票中分析
      </td>
      <td>
      解决方案，参见票中信息
      </td>
    </tr>


    <!--模块1--->
    <tr>
      <td> DSA/OTA升级失败 </td>
      <td> 升级失败，日志显示SCC没有升级 </td>
      <td>
      票：GEELY-41336 云端显示“Installation Finished”，OTA升级失败<br>
      原因SCC没有升级，doip_update.xml的中仅仅包含FX11没有FX11_HEV,导致没有升级SCC。
      </td>
      <td>
      解决方案，参见票中信息。<br>
      不支持FX11(燃油)和FX11_HEV（混动）之间的互生。<br>
      可以先U盘或者线刷一个版本（如FX11_HEV)，然后在升级FX11_HEV版本。
      </td>
    </tr>

    <!--模块1--->
    <tr>
      <td> DSA/OTA升级失败 </td>
      <td> 升级失败，安卓android启动失败 </td>
      <td>
      票：GEELY-29150、GEELY-22925 如果当前版本使用了non_secure mifs的image包，会导致安卓启动不起来，请谨慎使用。
      </td>
      <td>
      请用U盘升级到secure mifs的image包，恢复环境后再用DSA升级
      </td>
    </tr>




</table>
