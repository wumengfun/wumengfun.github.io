# commons-codec.jar包base64加密末尾出现换行符的问题

最近做sm2,sm4的加密开发时，发现在base64转码，转换后的字段末尾会出现\r\n，导致json报文解析存在问题，故对此问题进行分析。

## 源码分析

对比commons-codec-1.15.jar（末尾无\r\n）和commons-codec-1.4.jar（末尾会出现\r\n）两个版本，调用的都是encodeBase64String()方法，故对源码进行分析。

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610104534.png)

在commons-codec-1.4.jar中最终实现为下图方法

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610104903.png)

在commons-codec-1.15.jar中最终实现为下图方法

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610105033.png)

分别查看差异行Base64（）的实现方法发现差异

在commons-codec-1.4.jar中，CHUNK_SEPARATOR参数初始化为 new byte[]{13, 10}

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610105334.png)

根据ASCII码表查询可知

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610105522.png)

当base64加密超过76位时，会在末尾增加\r\n，符合出现的报错。

在commons-codec-1.15.jar中，CHUNK_SEPARATOR参数初始化为无参byte[]，所以当base64加密时长度超过76位时，不会增加\r\n。

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610105805.png)



## 资料查询

查阅相关资料发现在RFC2045中规定Base64超过76个字符后须添加回车换行符。

![](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220610110427.png)

## 解决方案

1. 使用高版本commons-codec-1.15.jar，jar包封装时不会做末尾添加处理
2. 使用低版本commons-codec-1.4.jar，在使用Base64.encodeBase64String(）时,添加调用replaceAll("\r|\n", "")，将补充的字符替换删除