

# centos7 安装 nfs



## server - 192.168.128.10

```
su root

systemctl stop firewalld.service
setenforce 0

su app

mkdir nfsfile
chmod -R 777 nfsfile

cd nfsfile
echo "some test" > test.txt

sudo vim /etc/exports

/home/app/nfsfile 192.168.128.*（rw,sync,root_squash)

# server install
sudo yum -y install rpcbind nfs-utils nfs-server

showmount [-adehv]
```





## client

```
sudo yum -y install rpcbind nfs-utils

showmount -e 192.168.128.10
sudo mount -t nfs 192.168.128.10:/home/app/nfsfile /home/app/nfsfile

cd /home/app/nfsfile
ls
cat test.txt
```

开机自动挂载：

```
echo "mount -t nfs 192.168.128.10:/home/app/nfsfile /home/app/nfsfile" >> /etc/rc.d/rc.local
```





