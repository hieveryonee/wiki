# VBF 专题

>1. [如何查看VBF头文件信息](#如何查看VBF头文件信息 "如何查看VBF头文件信息")
1. [如何解压VBF](#如何解压VBF "如何解压VBF")
1. [VBF制作过程](#VBF制作过程 "VBF制作过程")
1. [如何确认VBF是加签方式。加了哪种签？研发签？生产签？](#如何确认VBF是加签方式。加了哪种签？研发签？生产签？ "如何确认VBF是加签方式。加了哪种签？研发签？生产签？")
1. [如何用工具校验VBF是否完整](#如何用工具校验VBF是否完整 "如何用工具校验VBF是否完整")
1. [如何验证VBF中私钥和DHU上公钥是否匹配](#如何验证VBF中私钥和DHU上公钥是否匹配 "如何验证VBF中私钥和DHU上公钥是否匹配")
1. [Public Key](#Public Key "Public Key")
1. [pin code](#pin code "pin code")

## 如何查看VBF头文件信息

答：用notepad++ 直接发开VBF，即可以查看文件头信息

```

vbf_version = 2.6;
header {
	// Created by Geely VbfSign: 2022-12-18 22:22:38
	sw_part_number = "8895326513";
	sw_version = "A";
	sw_part_type = SBL;
	data_format_identifier = 0x00;
	ecu_address = 0x1202;
	call = 0x00000000;
	verification_block_start = 0x00000000;
	verification_block_length = 0x0000002C;
	verification_block_root_hash = 0x3F78E0606EABC512E1F25C38DB51806C0BC5346BB8D39446AB00E9EA3FA67901;

  file_checksum = 0x7B8C1AFF;

  // 研发签
	sw_signature_dev = 0x7FE3F4C69999BDFF126F3C7BF50DD23848E1E002E99E2A377AE5F0512A6935EEE41AEC5605C39D0207922B9A718301C0756EF71DCFEEE6ACDC7BD0AB2957191D483D1BFCB3A1061660A9C325AB81CAB72EAE346C5AC4E8030B25422EDA7AA95DA1981B6ED8874247AD3C24E52E89A3CF1571F95D261B443F4B340989505FBDC1CDAD0C8B055E39E583393902B773CD1FD315E495D1BB38DFF80F1741420EC708DE62917EA6CCF42F2D50138D37AFF80C0BC23F60585D7F64D4392F5AE57BA756F5DCC60D9E76FB6667888E598E1A46BFCB5A8C9D15971FF2289FBF03ECF42C71751EA82AE278F78432840695AE057A5E136CEFDB16821B630CD0812C60A8FB66;

  // 生产签
  sw_signature = 0x7FE3F4C69999BDFF126F3C7BF50DD23848E1E002E99E2A377AE5F0512A6935EEE41AEC5605C39D0207922B9A718301C0756EF71DCFEEE6ACDC7BD0AB2957191D483D1BFCB3A1061660A9C325AB81CAB72EAE346C5AC4E8030B25422EDA7AA95DA1981B6ED8874247AD3C24E52E89A3CF1571F95D261B443F4B340989505FBDC1CDAD0C8B055E39E583393902B773CD1FD315E495D1BB38DFF80F1741420EC708DE62917EA6CCF42F2D50138D37AFF80C0BC23F60585D7F64D4392F5AE57BA756F5DCC60D9E76FB6667888E598E1A46BFCB5A8C9D15971FF2289FBF03ECF42C71751EA82AE278F78432840695AE057A5E136CEFDB16821B630CD0812C60A8FB66;
}

```

## 如何解压VBF

答：  
方法1： 一般VBF中打包一个zip类型的Block数据，因此直接直接修改vbf文件为zip后缀，即可以直接打开解压。   
方法2： 通过工具 VBF_Check.rar，既可以校验，也可以提取数据  

[>>VBF_Check.rar](assets/faq/VBF_Check.rar)


## 如何确认VBF是加签方式。加了哪种签？研发签？生产签？

答：打开VBF的头信息，查看是否有对应字段：研发签[sw_signature_dev] 和 生产签 [sw_signature]

## 如何用工具校验VBF是否完整

答：可以用如下工具， [>>VBF_Check.rar](assets/faq/VBF_Check.rar) 。

该工具完全按照VBF的格式规范进行了解析。checksum级别的。只要工具校验通过，VBF就能保证格式正确。

## 如何验证VBF中私钥和DHU上公钥是否匹配

答：vbf加解密的介绍已经在 [业务知识讲解/VBF] 中介绍。通过该工具，可以验证，加解密是否匹配。

>需要的请单独找我获取

## Public Key

1、研发签和生产签，决定了DHU中必须存储对应的研发公钥和生成公钥   
2、默认情况DHU中已经存储了一个研发公钥，不需要用户在写入。生成公钥是在产线的SWDL工位通过DID方式写入   
3、当前吉利使用的生产私钥和公钥是固定的，即统一使用一对公钥、私钥   
4、SWDL也会写入password和cert证书，这个是每天车都不一样的，吉利云平台会统一维护这些密码，DHU通过这些密码和吉利云认证，完成安全认证   
5、如果DHU中写入了生产公钥，那么VBF必须要增加[生产签 sw_signature], 否则升级会验签失败   
6、公钥在DHU中存储形式：总长度 292 = 256(公钥) + 4(exponent) + 32(校验和)   

## pin code

1、pin code 是产线SWDL工位写入   
2、pin code用于过安全访问时用（27 01/02）   
即：DSA工具（或者诊断仪）必须知道对应对应DHU 的pin code，才能过27 服务   
3、pin code的相关计算方法参考 [电检中27 01和27 02安全访问算法] 章节   
