# 使用SourceTree提交github失败文件解决方法（Windows10）  

最近在使用SourceTree提交github时出现多种失败异常，将常见的几种异常及其解决方式做一个汇总分享记录。

## 问题1：OpenSSL SSL_read: Connection was reset, errno 10054  

问题截图  

![errno 10054](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/1642580165(1).png)  

解决办法

### 方法1：修改本地git项目的config配置文件  

    打开 本地git项目路径/.git/config 文件
    添加配置
        [http]
         sslVerify = false  

![本地config配置](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220119172316.png)  

### 方法2：修改本地git项目的ssl校验  

    在本地git项目目录下右键选择[git_bash_here]  
    git config http.sslVerify false  

### 方法3：修改全局的ssl校验  

    git config --global http.sslVerify false  

此问题解决方法参考于：<https://juejin.cn/post/7041207219978764296>  

## 问题2：Failed to connect to github.com port 443: Timed out  

问题截图  

![443: Timed out](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/1642584842(1).png)  

解决方法

### 第一步：打开下面三个网站并记录ip地址  

注：**使用你自己查询展示的IP地址，可能存在不通**  

    https://ipaddress.com/website/github.com  

![github.com ](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220119174227.png)  
  
    https://ipaddress.com/website/github.global.ssl.fastly.net#ipinfo  

![github.global.ssl.fastly.net](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220119174423.png)  

    https://ipaddress.com/website/assets-cdn.github.com  

![assets-cdn.github.com](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220119174618.png)

### 第二步：修改本地host文件  

Windows系统的host文件路径：C:\Windows\System32\drivers\etc\hosts  
建议使用Notepad++或VS Code打开，将host文件修改为下图  

    140.82.114.4  github.com
    199.232.69.194 github.global.ssl.fastly.net
    185.199.108.153 assets-cdn.github.com
    185.199.109.153 assets-cdn.github.com
    185.199.110.153 assets-cdn.github.com
    185.199.111.153 assets-cdn.github.com

![host文件配置](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220119181124.png)  

### 第三步：刷新DNS缓存  

    Windows图标-右键选择Windows PowerShell（管理员）  
    ipconfig /flushdns  

注：**可以多刷几次缓存**  

此问题解决方法参考于：<https://juejin.cn/post/6972373449549283336>  
