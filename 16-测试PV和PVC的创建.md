# 新建存储
+ 目前阿里云支持kubernetes的存储有2种：第一种是使用NAS数据卷，其实就是NFS，需要安装nfs-utils组件。第二种是阿里云盘，需要安装阿里云盘 driver rpm 包。
阿里云的帮助文档为：https://help.aliyun.com/document_detail/53754.html?spm=5176.doc59379.6.852.G6lt3O


+ 按照阿里云的帮助文档，此次采用NAS方式，后期看日志的时候将所有需要的日志挂载到任意一台机器上。
首先购买NAS数据卷，一次购买6个5G的NAS数据卷
kubernetes内所有需要访问pv的机器和挂载NAS的机器都需要安装nfs-utils。
```bash
[root@node03 ~]# yum install nfs-utils
Loaded plugins: fastestmirror
base                                                                                                                                                                    | 3.6 kB  00:00:00     
epel                                                                                                                                                                    | 4.7 kB  00:00:00     
extras                                                                                                                                                                  | 3.4 kB  00:00:00     
updates                                                                                                                                                                 | 3.4 kB  00:00:00     
(1/3): extras/7/x86_64/primary_db                                                                                                                                       | 145 kB  00:00:00     
(2/3): epel/x86_64/updateinfo                                                                                                                                           | 860 kB  00:00:00     
(3/3): epel/x86_64/primary_db                                                                                                                                           | 6.1 MB  00:00:00     
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package nfs-utils.x86_64 1:1.3.0-0.48.el7_4 will be installed
----------------------------------------------------------------------------------------------------
Installed:
  nfs-utils.x86_64 1:1.3.0-0.48.el7_4                                                                                                                                                          

Dependency Installed:
  gssproxy.x86_64 0:0.7.0-4.el7            keyutils.x86_64 0:1.5.8-3.el7      libbasicobjects.x86_64 0:0.1.1-27.el7   libcollection.x86_64 0:0.6.2-27.el7   libevent.x86_64 0:2.0.21-4.el7    
  libini_config.x86_64 0:1.3.0-27.el7      libnfsidmap.x86_64 0:0.25-17.el7   libpath_utils.x86_64 0:0.2.1-27.el7     libref_array.x86_64 0:0.1.5-27.el7    libtirpc.x86_64 0:0.2.4-0.10.el7  
  libverto-libevent.x86_64 0:0.2.5-4.el7   quota.x86_64 1:4.01-14.el7         quota-nls.noarch 1:4.01-14.el7          rpcbind.x86_64 0:0.2.0-42.el7         tcp_wrappers.x86_64 0:7.6-77.el7  

Complete!
```

然后创建pv
```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nas
spec:
  capacity:
    storage: 8Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    path: /
    server: 055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com
```
根据上面的文件来创建pv
