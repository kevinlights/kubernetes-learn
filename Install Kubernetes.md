





## DevOps 本地环境搭建





### 资料收集：



Kubernetes:





Git:





Jenkins





Docker:







#### Nexus:

https://www.trojansun.com/nexus3-install.html

https://www.cnblogs.com/yangshaoxiang/p/8807657.html







#### DevOps:

https://zhuanlan.zhihu.com/p/55066486

https://www.zhihu.com/question/55874411

https://zhuanlan.zhihu.com/p/57021068

https://zhuanlan.zhihu.com/p/55722122







### 环境准备

#### 操作系统

- CentOS-7-x86_64-Minimal-1908

##### 查看 ifconfig 无命令

https://www.cnblogs.com/cy60/p/9287856.html

原因：

安装时未启用网卡

```
cat /etc/sysconfig/network-scripts/ifcfg-ens33
```

可以看到 `ONBOOT=no`

将其修改为 `yes`，再重启

```
service network restart
yum install net-tools
```

> 建议先配置国内源
>
> https://www.jianshu.com/p/73824fde9328
>
> 如有需要可禁用 fastermirror 插件

##### 关闭 ssh UseDNS

https://blog.csdn.net/doiido/article/details/43793391

解决 ssh 连接慢的问题

##### 配置 sudoer

```
cd /etc/
vi sudoers
```

https://www.bilibili.com/read/cv7211002

```
cd /etc/
cat sudoers
sudo usermod -aG wheel app
```

##### network

https://blog.csdn.net/u013305747/article/details/78375946

```
vi /etc/resolv.conf 

nameserver 8.8.8.8
nameserver 8.8.4.4
```



#### Kubernetes

##### ~~安装 （未成功）~~

https://www.bookstack.cn/read/minikube-1.18-en/511d79b31568113e.md



##### 选择平台

###### docker

https://docs.docker.com/engine/install/centos/

```
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io
```

也可以选择版本进行安装：

```
yum list docker-ce --showduplicates | sort -r
sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

启动：

```
sudo systemctl start docker
```

配置 docker 源

```
cd /etc/docker/
ls
sudo vim daemon.json

{
    "registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}

sodu systemctl daemon-reload
sudo systemctl restart docker


sudo groupadd docker
sudo usermod -aG docker $USER
reboot

sudo systemctl enable docker
docker info

```

##### 配置环境变量

```
vim .bashrc

export PATH=%PATH:/usr/local/bin/

source .bashrc
```

##### 安装 minikute

```
 curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
 sudo install minikube-linux-amd64 /usr/local/bin/minikube


curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube

minikube config set driver docker

minikube start
```

如果在国内，建议使用以下命令：

https://zhuanlan.zhihu.com/p/112755080

```
minikube start --image-repository='registry.cn-hangzhou.aliyuncs.com/google_containers'
```

```
minikube start --image-repository='http://f1361db2.m.daocloud.io'
```



#### 国内安装 minikube 参考：



使用阿里云提供的工具来部署

##### 安装 kubectl

```

cd /etc/yum.repos.d/
sudo vim kubernetes.repo

[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg


sudo yum install -y kubectl

kubectl version

Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.8", GitCommit:"9f2892aab98fe339f3bd70e3c470144299398ace", GitTreeState:"clean", BuildDate:"2020-08-13T16:12:48Z", GoVersion:"go1.13.15", Compiler:"gc", Platform:"linux/amd64"}
The connection to the server localhost:8080 was refused - did you specify the right host or port?


```

##### 安装 minikube

```


curl -Lo minikube http://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/v1.18.1/minikube-linux-amd64

chmod +x minikube
mv minikube /usr/local/bin/
minikube version

minikube version: v1.18.1
commit: 511aca80987826051cf1c6527c3da706925f7909

minikube start --driver=none --registry-mirror=https://registry.docker-cn.com

```

> * minikube v1.18.1 on Centos 7.7.1908
> * Using the none driver based on user configuration
>
> X Exiting due to GUEST_MISSING_CONNTRACK: Sorry, Kubernetes 1.20.2 requires conntrack to be installed in root's path

```
sudo yum install -y conntrack

# minikube start --driver=none --registry-mirror=https://registry.docker-cn.com

# 或 

minikube start --driver=none --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers
```

> ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables contents are not set to 1
> [preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
> To see the stack trace of this error execute with --v=5 or higher

```
[app@localhost tmp]$ cd /proc/sys/net/bridge/
[app@localhost bridge]$ ls
bridge-nf-call-arptables  bridge-nf-call-iptables        bridge-nf-filter-vlan-tagged
bridge-nf-call-ip6tables  bridge-nf-filter-pppoe-tagged  bridge-nf-pass-vlan-input-dev
[app@localhost bridge]$ ll
total 0
-rw-r--r--. 1 root root 0 Apr 19 17:50 bridge-nf-call-arptables
-rw-r--r--. 1 root root 0 Apr 19 17:50 bridge-nf-call-ip6tables
-rw-r--r--. 1 root root 0 Apr 19 17:50 bridge-nf-call-iptables
-rw-r--r--. 1 root root 0 Apr 19 17:59 bridge-nf-filter-pppoe-tagged
-rw-r--r--. 1 root root 0 Apr 19 17:59 bridge-nf-filter-vlan-tagged
-rw-r--r--. 1 root root 0 Apr 19 17:59 bridge-nf-pass-vlan-input-dev
[app@localhost bridge]$ cat bridge-nf-call-iptables
0
[app@localhost bridge]$ cat bridge-nf-call-ip6tables
0

[app@localhost bridge]$ su root
Password:
[root@localhost bridge]# ll
total 0
-rw-r--r--. 1 root root 0 Apr 19 17:50 bridge-nf-call-arptables
-rw-r--r--. 1 root root 0 Apr 19 17:50 bridge-nf-call-ip6tables
-rw-r--r--. 1 root root 0 Apr 19 17:50 bridge-nf-call-iptables
-rw-r--r--. 1 root root 0 Apr 19 17:59 bridge-nf-filter-pppoe-tagged
-rw-r--r--. 1 root root 0 Apr 19 17:59 bridge-nf-filter-vlan-tagged
-rw-r--r--. 1 root root 0 Apr 19 17:59 bridge-nf-pass-vlan-input-dev
[root@localhost bridge]# echo 1 > bridge-nf-call-iptables
[root@localhost bridge]# echo 0 > bridge-nf-call-ip6tables

# 仍然在 root 用户下
[root@localhost bridge]# minikube start --driver=none --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers


```

> * minikube v1.18.1 on Centos 7.7.1908
> * Using the none driver based on user configuration
>
> X Requested memory allocation (1819MB) is less than the recommended minimum 1900MB. Deployments may fail.
>
>
> X The requested memory allocation of 1819MiB does not leave room for system overhead (total system memory: 1819MiB). You may face stability issues.
> * Suggestion: Start minikube with less memory allocated: 'minikube start --memory=1819mb'
>
> * Using image repository registry.cn-hangzhou.aliyuncs.com/google_containers
> * Starting control plane node minikube in cluster minikube
> * Running on localhost (CPUs=2, Memory=1819MB, Disk=51175MB) ...
> * OS release is CentOS Linux 7 (Core)
> * Preparing Kubernetes v1.20.2 on Docker 20.10.6 ...
>   - Generating certificates and keys ...
>   - Booting up control plane ...
>   - Configuring RBAC rules ...
> * Configuring local host environment ...
> *
> ! The 'none' driver is designed for experts who need to integrate with an existing VM
> * Most users should use the newer 'docker' driver instead, which does not require root!
> * For more information, see: https://minikube.sigs.k8s.io/docs/reference/drivers/none/
> *
> ! kubectl and minikube configuration will be stored in /root
> ! To use kubectl or minikube commands as your own user, you may need to relocate them. For example, to overwrite your own settings, run:
> *
>   - sudo mv /root/.kube /root/.minikube $HOME
>   - sudo chown -R $USER $HOME/.kube $HOME/.minikube
> *
> * This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
> * Verifying Kubernetes components...
>   - Using image registry.cn-hangzhou.aliyuncs.com/google_containers/storage-provisioner:v4 (global image repository)
> * Enabled addons: storage-provisioner, default-storageclass
> * Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default



```
或在 app 用户下 
sudo cp /usr/local/bin/minikube /usr/bin/
sudo echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
sudo echo 1 > /proc/sys/net/bridge/bridge-nf-call-ip6tables
sudo minikube start --driver=none --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers

minikube status
```

> minikube
> type: Control Plane
> host: Running
> kubelet: Running
> apiserver: Running
> kubeconfig: Configured
> timeToStop: Nonexistent

```
[app@control-plane ~]$ sudo kubectl cluster-info
```

> Kubernetes control plane is running at https://192.168.128.128:8443
> KubeDNS is running at https://192.168.128.128:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
>
> To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.



## Learn kubernetes

[Kubernetes](./Kubernetes.md)

