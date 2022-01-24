# oralce磁盘满占用导致sqlpuls无法登录  

今天在系统测试时发现数据库连接失败，ssh连接上数据库服务器发现磁盘空间占用100%，sqlplus也无法进行登录操作，只能找dba进行磁盘扩容。  

![oralce磁盘满占用](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/e451c62e2c240ac8094cd77ffb6b9f6.png)  

在磁盘扩容完成后,登录数据库查看文件使用情况。  

     --查询数据文件个数和大小
    select COUNT(*),sum(bytes)/1024/1024/1024 GB from v$datafile;
    select COUNT(*),SUM(bytes)/1024/1024/1024 GB from DBA_DATA_FILES;  

![文件使用情况](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220124110616.png)  

发现数据文件占用只有5GB，但是磁盘显示的占用的30GB，感觉怪怪的(；′⌒`)  
查看一下其他环境的数据库情况。  

![正常运行的数据库数据](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220124112007.png)  

对比一下，很明显存在问题，感觉可能是数据库日志占用的问题。到服务器对应的路径下查看一下的文件大小。  

    du -h --max-depth=1  

![文件大小](https://wumeng-figurebed.oss-cn-shanghai.aliyuncs.com/images/20220124160249.png)  

与其他数据库服务器对比发现diag文件夹的占用明显特别大，查询发现diag目录下存储的是日志文件。继续查询此路径下的文件占用情况，发现下面四个日志文件夹的占用较大，基本确认问题是由于数据库日志的占用引起的。  

    /u01/app/oracle/diag/rdbms/Schema/Schema/trace  
    /u01/app/oracle/diag/rdbms/Schema/Schema/incident  
    /u01/app/oracle/diag/tnslsnr/localhost/listener/trace  
    /u01/app/oracle/diag/tnslsnr/localhost/listener/alert  

通知dba定期删除数据库日志就解决了( •̀ ω •́ )✧  

手动删除可以参考这个教程  
<https://blog.csdn.net/u010033674/article/details/106238817>  
