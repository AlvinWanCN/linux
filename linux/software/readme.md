<p align='center' >
  <a href='https://github.com/alvinwancn' target="_blank"> 
      <img src='https://github.com/AlvinWanCN/life-record/raw/master/images/etlucency.png' alt='Alvin Wan' width=250>
  </a>
</p>
<p align=center ><b>Common Network Yum Repository</b></p>

### 常见的一些网络yum源

```text
http://mirror.bit.edu.cn/centos/
http://mirrors.aliyun.com/centos/
http://mirrors.zju.edu.cn/centos/
http://mirror.lzu.edu.cn/centos/
http://mirrors.sohu.com/centos/
http://mirrors.tuna.tsinghua.edu.cn/centos/
http://mirrors.cn99.com/centos/
http://mirrors.cqu.edu.cn/CentOS/
http://mirrors.nju.edu.cn/centos/
http://mirrors.163.com/centos/
```
---
### 使用网络yum源
---

安装好系统之后，我们都会有默认的yum源，执行yum repolist 可以查看当前我们系统所使用的yum源。
```bash
[root@openstack ~]# ll /etc/yum.repos.d/
total 28
-rw-r--r--. 1 root root 1664 Aug 30 11:53 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Aug 30 11:53 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Aug 30 11:53 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  314 Aug 30 11:53 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Aug 30 11:53 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 Aug 30 11:53 CentOS-Sources.repo
-rw-r--r--. 1 root root 3830 Aug 30 11:53 CentOS-Vault.repo
```
这里我们查看一下其中一个文件CentOS-Base.repo的内容

```bash
[root@openstack ~]# grep -vE "^#|^$" /etc/yum.repos.d/CentOS-Base.repo 
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
[updates]
name=CentOS-$releasever - Updates
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
[extras]
name=CentOS-$releasever - Extras
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
[centosplus]
name=CentOS-$releasever - Plus
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```
我们会发现，配置文件的文件里有一行是mirrorlist,而我们平时自己配置本地yum仓库时，那里写的是baseurl，而不是mirrorlist，mirrorlsit的值是一个http链接，我们看到配置文件里这个链接中包含了一些变量，会根据不同的系统，而变为相应的内容。Alvin研究测试后发现，比如http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
 这个链接，最终这里面的变量取到值后，真实访问的完整链接是：http://mirror.bit.edu.cn/centos/7.4.1708/extras/x86_64/ ，而这个地址里的内容，则如下所示：
```text
http://mirror.bit.edu.cn/centos/7.4.1708/extras/x86_64/
http://mirrors.aliyun.com/centos/7.4.1708/extras/x86_64/
http://mirrors.zju.edu.cn/centos/7.4.1708/extras/x86_64/
http://mirror.lzu.edu.cn/centos/7.4.1708/extras/x86_64/
http://mirrors.sohu.com/centos/7.4.1708/extras/x86_64/
http://mirrors.tuna.tsinghua.edu.cn/centos/7.4.1708/extras/x86_64/
http://mirrors.cn99.com/centos/7.4.1708/extras/x86_64/
http://mirrors.cqu.edu.cn/CentOS/7.4.1708/extras/x86_64/
http://mirrors.nju.edu.cn/centos/7.4.1708/extras/x86_64/
http://mirrors.163.com/centos/7.4.1708/extras/x86_64/
```
以此判断，改yum仓库的配置，mirrorlist的配置，实际上是去找上门这个地址列表里的内容。