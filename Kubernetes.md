# Kubernetes Learn



https://kubernetes.io/docs/tutorials/kubernetes-basics/

https://kubernetes.io/zh/docs/tutorials

## create cluster

### start minikube

```
minikube version
```

> minikube version: v1.18.0
> commit: ec61815d60f66a6e4f6353030a40b12362557caa-dirty

```
minikube start
```

```
* minikube v1.18.0 on Ubuntu 18.04 (amd64)
* Using the none driver based on existing profile

X The requested memory allocation of 2200MiB does not leave room for system overhead (total system memory: 2460MiB). You may face stability issues.
* Suggestion: Start minikube with less memory allocated: 'minikube start --memory=2200mb'

* Starting control plane node minikube in cluster minikube
* Running on localhost (CPUs=2, Memory=2460MB, Disk=194868MB) ...
* OS release is Ubuntu 18.04.5 LTS
* Preparing Kubernetes v1.20.2 on Docker 19.03.13 ...
  - kubelet.resolv-conf=/run/systemd/resolve/resolv.conf
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
* Configuring local host environment ...
* Verifying Kubernetes components...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v4
* Enabled addons: storage-provisioner, default-storageclass
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```





### kubectl

```
kubectl version
```

>Client Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.4", GitCommit:"e87da0bd6e03ec3fea7933c4b5263d151aafd07c", GitTreeState:"clean", BuildDate:"2021-02-18T16:12:00Z", GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"}
>Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.2", GitCommit:"faecb196815e248d3ecfb03c680a4507229c2a56", GitTreeState:"clean", BuildDate:"2021-01-13T13:20:00Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}



```
kubectl cluster-info
```

>Kubernetes control plane is running at https://172.17.0.68:8443
>KubeDNS is running at https://172.17.0.68:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```
kubectl get nodes
```

>NAME       STATUS   ROLES                  AGE     VERSION
>minikube   Ready    control-plane,master   6m29s   v1.20.2

## deploy app



```
kubectl get nodes --help
```



```
kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```

> deployment.apps/kubernetes-bootcamp created

```
kubectl get deployments
```

> NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
> kubernetes-bootcamp   1/1     1            1           48s

```
kubectl proxy

starting to serve on 127.0.0.1:8001
```



```
curl http://127.0.0.1:8001/version
```

>{
>"major": "1",
>"minor": "20",
>"gitVersion": "v1.20.2",
>"gitCommit": "faecb196815e248d3ecfb03c680a4507229c2a56",
>"gitTreeState": "clean",
>"buildDate": "2021-01-13T13:20:00Z",
>"goVersion": "go1.15.5",
>"compiler": "gc",
>"platform": "linux/amd64"
>}



```
export POD_NAME=$(kubectl get pods -o go-template --template '{{range.items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
```

> Name of the Pod: kubernetes-bootcamp-57978f5f5d-5k8q4

```
kubectl get pods
```

> NAME                                   READY   STATUS             RESTARTS   AGE
> kubernetes-bootcamp-57978f5f5d-jk26s   0/1     ImagePullBackOff   0          8h

```
kubectl describe pods
```

> Name:         kubernetes-bootcamp-57978f5f5d-jk26s
> Namespace:    default
> Priority:     0
> Node:         control-plane.minikube.internal/192.168.128.128
> Start Time:   Mon, 19 Apr 2021 18:35:26 -0400
> Labels:       app=kubernetes-bootcamp
>               pod-template-hash=57978f5f5d
> Annotations:  <none>
> Status:       Pending
> IP:           172.17.0.2
> IPs:
>   IP:           172.17.0.2
> Controlled By:  ReplicaSet/kubernetes-bootcamp-57978f5f5d
> Containers:
>   kubernetes-bootcamp:
>     Container ID:
>     Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
>     Image ID:
>     Port:           <none>
>     Host Port:      <none>
>     State:          Waiting
>       Reason:       ImagePullBackOff
>     Ready:          False
>     Restart Count:  0
>     Environment:    <none>
>     Mounts:
>       /var/run/secrets/kubernetes.io/serviceaccount from default-token-tqqkh (ro)
> Conditions:
>   Type              Status
>   Initialized       True
>   Ready             False
>   ContainersReady   False
>   PodScheduled      True
> Volumes:
>   default-token-tqqkh:
>     Type:        Secret (a volume populated by a Secret)
>     SecretName:  default-token-tqqkh
>     Optional:    false
> QoS Class:       BestEffort
> Node-Selectors:  <none>
> Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
>                  node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
> Events:
>   Type     Reason     Age                    From               Message
>
> ----     ------     ----                   ----               -------
>   Normal   Scheduled  8h                     default-scheduler  Successfully assigned default/kubernetes-bootcamp-57978f5f5d-jk26s to control-plane.minikube.internal
>   Normal   Pulling    8h                     kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Normal   Pulling    30m (x4 over 33m)      kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Warning  Failed     30m (x4 over 32m)      kubelet            Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v1": rpc error: code = Unknown desc = Error response from daemon: Get https://gcr.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
>   Warning  Failed     30m (x4 over 32m)      kubelet            Error: ErrImagePull
>   Normal   BackOff    28m (x13 over 32m)     kubelet            Back-off pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Warning  Failed     8m10s (x98 over 32m)   kubelet            Error: ImagePullBackOff
>   Warning  Failed     6m47s                  kubelet            Error: ErrImagePull
>   Warning  Failed     6m47s                  kubelet            Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v1": rpc error: code = Unknown desc = Error response from daemon: Get https://gcr.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
>   Normal   BackOff    6m46s                  kubelet            Back-off pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Warning  Failed     6m46s                  kubelet            Error: ImagePullBackOff
>   Normal   Pulling    6m35s (x2 over 7m2s)   kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Normal   Pulling    3m30s (x4 over 5m48s)  kubelet            Pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Warning  Failed     3m15s (x4 over 5m33s)  kubelet            Failed to pull image "gcr.io/google-samples/kubernetes-bootcamp:v1": rpc error: code = Unknown desc = Error response from daemon: Get https://gcr.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
>   Warning  Failed     3m15s (x4 over 5m33s)  kubelet            Error: ErrImagePull
>   Normal   BackOff    3m (x6 over 5m33s)     kubelet            Back-off pulling image "gcr.io/google-samples/kubernetes-bootcamp:v1"
>   Warning  Failed     36s (x15 over 5m33s)   kubelet            Error: ImagePullBackOff



```
kubectl proxy
curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-bootcamp-57978f5f5d-jk26s/proxy -i
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
```

> HTTP/1.1 301 Moved Permanently
>   Cache-Control: no-cache, private
>   Content-Length: 0
>   Date: Tue, 20 Apr 2021 07:39:23 GMT
>Location: /api/v1/namespaces/default/pods/kubernetes-bootcamp-57978f5f5d-jk26s/proxy/

```
curl http://localhost:8001/api/v1/namespaces/default/pods/kubernetes-bootcamp-57978f5f5d-jk26s/proxy/ -i
```

> HTTP/1.1 500 Internal Server Error
> Cache-Control: no-cache, private
> Content-Length: 207
> Content-Type: application/json
> Date: Tue, 20 Apr 2021 07:42:11 GMT
>
> {
>   "kind": "Status",
>   "apiVersion": "v1",
>   "metadata": {
>
>   },
>   "status": "Failure",
>   "message": "error trying to reach service: dial tcp 172.17.0.2:80: connect: connection refused",
>   "code": 500
> }

```
kubectl logs kubernetes-bootcamp-57978f5f5d-jk26s
```

> Error from server (BadRequest): container "kubernetes-bootcamp" in pod "kubernetes-bootcamp-57978f5f5d-jk26s" is waiting to start: trying and failing to pull image

### install echoserver

```
kubectl create deployment hello-minikube --image=registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10
```



```
[root@control-plane app]# kubectl get pods
NAME                                   READY   STATUS             RESTARTS   AGE
hello-minikube-8947b65df-sthwj         1/1     Running            0          25s
kubernetes-bootcamp-57978f5f5d-jk26s   0/1     ImagePullBackOff   0          9h
[root@control-plane app]# kubectl delete pods
error: resource(s) were provided, but no name was specified
[root@control-plane app]# kubectl delete pods kubernetes-bootcamp-57978f5f5d-jk26s
pod "kubernetes-bootcamp-57978f5f5d-jk26s" deleted

```

```
kubectl describe pods hello-minikube-8947b65df-sthwj
```

> Name:         hello-minikube-8947b65df-sthwj
> Namespace:    default
> Priority:     0
> Node:         control-plane.minikube.internal/192.168.128.128
> Start Time:   Tue, 20 Apr 2021 04:09:34 -0400
> Labels:       app=hello-minikube
>               pod-template-hash=8947b65df
> Annotations:  <none>
> Status:       Running
> IP:           172.17.0.6
> IPs:
>   IP:           172.17.0.6
> Controlled By:  ReplicaSet/hello-minikube-8947b65df
> Containers:
>   echoserver:
>     Container ID:   docker://cbe3de09c950d56d729881b24094b2c78f4e08c9a90c32196ccf746eff558ea5
>     Image:          registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10
>     Image ID:       docker-pullable://registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver@sha256:cb5c1bddd1b5665e1867a7fa1b5fa843a47ee433bbb75d4293888b71def53229
>     Port:           <none>
>     Host Port:      <none>
>     State:          Running
>       Started:      Tue, 20 Apr 2021 04:09:43 -0400
>     Ready:          True
>     Restart Count:  0
>     Environment:    <none>
>     Mounts:
>       /var/run/secrets/kubernetes.io/serviceaccount from default-token-tqqkh (ro)
> Conditions:
>   Type              Status
>   Initialized       True
>   Ready             True
>   ContainersReady   True
>   PodScheduled      True
> Volumes:
>   default-token-tqqkh:
>     Type:        Secret (a volume populated by a Secret)
>     SecretName:  default-token-tqqkh
>     Optional:    false
> QoS Class:       BestEffort
> Node-Selectors:  <none>
> Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
>                  node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
> Events:
>   Type    Reason     Age    From               Message
>
> ----    ------     ----   ----               -------
>   Normal  Scheduled  4m28s  default-scheduler  Successfully assigned default/hello-minikube-8947b65df-sthwj to control-plane.minikube.internal
>   Normal  Pulling    4m28s  kubelet            Pulling image "registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10"
>   Normal  Pulled     4m21s  kubelet            Successfully pulled image "registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10" in 6.635159724s
>   Normal  Created    4m20s  kubelet            Created container echoserver
>   Normal  Started    4m20s  kubelet            Started container echoserver

```
kubectl get pods -o wide
```

> NAME                                   READY   STATUS             RESTARTS   AGE     IP           NODE                                                                                                     NOMINATED NODE   READINESS GATES
> hello-minikube-8947b65df-sthwj         1/1     Running            0          6m19s   172.17.0.6   control-plane.minikube.interna                                                                       l   <none>           <none>
> kubernetes-bootcamp-57978f5f5d-pcspw   0/1     ImagePullBackOff   0          5m34s   172.17.0.7   control-plane.minikube.interna   

```
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

> service/hello-minikube exposed

```
kubectl get pods -o wide
```

> NAME                                   READY   STATUS             RESTARTS   AGE     IP           NODE                              NOMINATED NODE   READINESS GATES
> hello-minikube-8947b65df-sthwj         1/1     Running            0          7m57s   172.17.0.6   control-plane.minikube.internal   <none>           <none>
> kubernetes-bootcamp-57978f5f5d-pcspw   0/1     ImagePullBackOff   0          7m12s   172.17.0.7   control-plane.minikube.internal   <none>           <none>

```
kubectl describe pod hello-minikube
```

> Name:         hello-minikube-8947b65df-sthwj
> Namespace:    default
> Priority:     0
> Node:         control-plane.minikube.internal/192.168.128.128
> Start Time:   Tue, 20 Apr 2021 04:09:34 -0400
> Labels:       app=hello-minikube
>               pod-template-hash=8947b65df
> Annotations:  <none>
> Status:       Running
> IP:           172.17.0.6
> IPs:
>   IP:           172.17.0.6
> Controlled By:  ReplicaSet/hello-minikube-8947b65df
> Containers:
>   echoserver:
>     Container ID:   docker://cbe3de09c950d56d729881b24094b2c78f4e08c9a90c32196ccf746eff558ea5
>     Image:          registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10
>     Image ID:       docker-pullable://registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver@sha256:cb5c1bddd1b5665e1867a7fa1b5fa843a47ee433bbb75d4293888b71def53229
>     Port:           <none>
>     Host Port:      <none>
>     State:          Running
>       Started:      Tue, 20 Apr 2021 04:09:43 -0400
>     Ready:          True
>     Restart Count:  0
>     Environment:    <none>
>     Mounts:
>       /var/run/secrets/kubernetes.io/serviceaccount from default-token-tqqkh (ro)
> Conditions:
>   Type              Status
>   Initialized       True
>   Ready             True
>   ContainersReady   True
>   PodScheduled      True
> Volumes:
>   default-token-tqqkh:
>     Type:        Secret (a volume populated by a Secret)
>     SecretName:  default-token-tqqkh
>     Optional:    false
> QoS Class:       BestEffort
> Node-Selectors:  <none>
> Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
>                  node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
> Events:
>   Type    Reason     Age    From               Message
> ----    ------     ----   ----               -------
>   Normal  Scheduled  9m48s  default-scheduler  Successfully assigned default/hello-minikube-8947b65df-sthwj to control-plane.minikube.internal
>   Normal  Pulling    9m48s  kubelet            Pulling image "registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10"
>   Normal  Pulled     9m41s  kubelet            Successfully pulled image "registry.cn-hangzhou.aliyuncs.com/google_containers/echoserver:1.10" in 6.635159724s
>   Normal  Created    9m40s  kubelet            Created container echoserver
>   Normal  Started    9m40s  kubelet            Started container echoserver

```
kubectl get deployment
```

> NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
> hello-minikube        1/1     1            1           10m
> kubernetes-bootcamp   0/1     1            0           9h

```
kubectl scale deployments/hello-minikube --replicas=3
```



```
minikube service hello-minikube 
minikube service hello-minikube --url
```


> | NAMESPACE   | NAME             | TARGET PORT   | URL                            |
> | ----------- | ---------------- | ------------- | ------------------------------ |
> | default     | hello-minikube   | 8080          | http://192.168.128.128:32298   |
> | ----------- | ---------------- | ------------- | ------------------------------ |
> * Opening service default/hello-minikube in default browser...
>   - http://192.168.128.128:32298

```
kubectl delete services hello-minikube
kubectl delete deployment hello-minikube
```

**删除 service 不会删除  pod，删除 deployment 后 pod 会被删除**

```
minikube dashboard
```

> * Verifying dashboard health ...
> * Launching proxy ...
> * Verifying proxy health ...
> http://127.0.0.1:44021/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/

```
kubectl logs hello-minikube-8947b65df-sthwj
```



```
kubectl get pod -n kube-system
```

> NAME                                                      READY   STATUS    RESTARTS   AGE
> coredns-54d67798b7-cdknt                                  1/1     Running   4          10h
> etcd-control-plane.minikube.internal                      1/1     Running   5          10h
> kube-apiserver-control-plane.minikube.internal            1/1     Running   5          10h
> kube-controller-manager-control-plane.minikube.internal   1/1     Running   5          10h
> kube-proxy-scwzq                                          1/1     Running   4          10h
> kube-scheduler-control-plane.minikube.internal            1/1     Running   5          10h
> storage-provisioner                                       1/1     Running   7          10h

## knowledges

### Cluster

计算、存储和网络资源的集合

### Master

是 cluster 的大脑，主要负责调度，决定将应用放在哪里运行，可运行多个 master 来实现高可用

### Node

运行容器应用，由 master 管理，监控并汇报容器的状态，同时根据 master 的要求管理容器的生命周期

### Pod

pod 中所有容器使用同一个网络 namespace，即相同的 ip 地址和 port 空间，可以直接用 localhost 通信，共享存储。

Kubernetes 管理的是 pod 而不是直接管理容器

常见 one-container-per-pod

### Controller

管理 pod

#### Deployment

#### ReplicaSet

#### DaemonSet

#### StatefuleSet

#### Job



### Service

有自己的 IP 和端口，为 pod 提供了负载均衡

### Namespace

```
kubectl get namespace
```

> NAME                   STATUS   AGE
> default                Active   2d10h
> kube-node-lease        Active   2d10h
> kube-public            Active   2d10h
> kube-system            Active   2d10h
> kubernetes-dashboard   Active   2d9h

default：创建资源时如果不指定，将被放到这个Namespace中。

kube-system：Kubernetes自己创建的系统资源将放到这个Namespace中。

## Architecture

### Master

#### kube-apiserver

#### kube-scheduler

#### kube-controller-manager

#### etcd

保存配置信息和各种资源的状态信息，变化时通知相关组件

#### pod 网络（如 flannel）

### Node

#### kubelet

根据 scheduler 发送的配置信息（image, volume等）创建和运行容器，并向 Master 报告运行状态

#### kube-proxy

负责将访问 service 的 tcp/udp 数据流转发到后端的容器，以及负载均衡

#### pod 网络（如 flannel）

```
kubectl get pod --all-namespaces -o wide
```

> NAMESPACE              NAME                                                      READY   STATUS    RESTARTS   AGE     IP                NODE                              NOMINATED NODE   READINESS GATES
> default                hello-minikube-8947b65df-sthwj                            1/1     Running   2          2d      172.17.0.3        control-plane.minikube.internal   <none>           <none>
> kube-system            coredns-54d67798b7-cdknt                                  1/1     Running   6          2d10h   172.17.0.2        control-plane.minikube.internal   <none>           <none>
> kube-system            etcd-control-plane.minikube.internal                      1/1     Running   7          2d10h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-system            kube-apiserver-control-plane.minikube.internal            1/1     Running   7          2d10h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-system            kube-controller-manager-control-plane.minikube.internal   1/1     Running   7          2d10h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-system            kube-proxy-scwzq                                          1/1     Running   6          2d10h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-system            kube-scheduler-control-plane.minikube.internal            1/1     Running   7          2d10h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-system            storage-provisioner                                       1/1     Running   11         2d10h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kubernetes-dashboard   dashboard-metrics-scraper-7886f6d855-4jp9l                1/1     Running   6          2d10h   172.17.0.5        control-plane.minikube.internal   <none>           <none>
> kubernetes-dashboard   kubernetes-dashboard-66f6c8f7c5-r78wr                     1/1     Running   6          2d10h   172.17.0.4        control-plane.minikube.internal   <none>           <none>



```
systemctl status kubelet.service
```

> ● kubelet.service - kubelet: The Kubernetes Node Agent
>    Loaded: loaded (/usr/lib/systemd/system/kubelet.service; disabled; vendor preset: disabled)
>   Drop-In: /etc/systemd/system/kubelet.service.d
>            └─10-kubeadm.conf
>    Active: active (running) since Thu 2021-04-22 04:29:49 EDT; 17min ago
>      Docs: http://kubernetes.io/docs/
>  Main PID: 2499 (kubelet)
>     Tasks: 16
>    Memory: 154.6M
>    CGroup: /system.slice/kubelet.service
>            └─2499 /var/lib/minikube/binaries/v1.20.2/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime=docker --hostna...
>
> Apr 22 04:35:23 control-plane.minikube.internal kubelet[2499]: I0422 04:35:23.946132    2499 reconciler.go:319] Volume detached for volume "default-token-tqqkh" (UniqueName: "kubernetes...vicePath ""
> Apr 22 04:35:24 control-plane.minikube.internal kubelet[2499]: E0422 04:35:24.807864    2499 kuberuntime_container.go:662] killContainer "echoserver"(id={"docker" "4886a90298ce4bf09f0dd72d9a610679...
> Apr 22 04:35:24 control-plane.minikube.internal kubelet[2499]: E0422 04:35:24.807919    2499 kuberuntime_container.go:662] killContainer "echoserver"(id={"docker" "e030ed7f32050381806fb80905556f8b...
> Apr 22 04:35:24 control-plane.minikube.internal kubelet[2499]: W0422 04:35:24.810261    2499 docker_sandbox.go:240] Both sandbox container and checkpoint for id "99b3d8f7318db845b79e2af...nformation.
> Apr 22 04:35:24 control-plane.minikube.internal kubelet[2499]: W0422 04:35:24.810324    2499 docker_sandbox.go:240] Both sandbox container and checkpoint for id "2717f85404b9f3e9ebbfb97...nformation.
> Apr 22 04:35:24 control-plane.minikube.internal kubelet[2499]: E0422 04:35:24.813474    2499 kubelet_pods.go:1256] Failed killing the pod "hello-minikube-8947b65df-fm6dt": failed to "KillContainer...
> Apr 22 04:35:24 control-plane.minikube.internal kubelet[2499]: E0422 04:35:24.813853    2499 kubelet_pods.go:1256] Failed killing the pod "hello-minikube-8947b65df-2dstx": failed to "KillContainer...
> Apr 22 04:35:25 control-plane.minikube.internal kubelet[2499]: I0422 04:35:25.856481    2499 reconciler.go:196] operationExecutor.UnmountVolume started for volume "default-token-tqqkh" (UniqueName...
> Apr 22 04:35:25 control-plane.minikube.internal kubelet[2499]: I0422 04:35:25.863340    2499 operation_generator.go:797] UnmountVolume.TearDown succeeded for volume "kubernetes.io/secret/9d8ecf25-...
> Apr 22 04:35:25 control-plane.minikube.internal kubelet[2499]: I0422 04:35:25.956829    2499 reconciler.go:319] Volume detached for volume "default-token-tqqkh" (UniqueName: "kubernetes...vicePath ""
> Hint: Some lines were ellipsized, use -l to show in full.



### sample

```
kubectl run httpd-app --image=httpd --replicas=2
```

> pod/httpd-app created

```
kubectl create deployment httpd-app --image=httpd --replicas=2
```

> deployment.apps/httpd-app created



## Run App

### Deployment

#### command

```
kubectl explain deployment

kubectl run nginx-deployment --image=nginx:1.7.9
```

> pod/nginx-deployment created

```
kubectl create deployment nginx-deployment --image=nginx:1.7.9 --replicas=2
```



```
kubectl describe deployment nginx-deployment
```

> Name:                   nginx-deployment
> Namespace:              default
> CreationTimestamp:      Sat, 24 Apr 2021 02:05:13 -0400
> Labels:                 app=nginx-deployment
> Annotations:            deployment.kubernetes.io/revision: 1
> Selector:               app=nginx-deployment
> Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
> StrategyType:           RollingUpdate
> MinReadySeconds:        0
> RollingUpdateStrategy:  25% max unavailable, 25% max surge
> Pod Template:
>   Labels:  app=nginx-deployment
>   Containers:
>    nginx:
>     Image:        nginx:1.7.9
>     Port:         <none>
>     Host Port:    <none>
>     Environment:  <none>
>     Mounts:       <none>
>   Volumes:        <none>
> Conditions:
>   Type           Status  Reason
>
> ----           ------  ------
>   Available      True    MinimumReplicasAvailable
>   Progressing    True    NewReplicaSetAvailable
> OldReplicaSets:  <none>
> NewReplicaSet:   nginx-deployment-84b896cf65 (2/2 replicas created)
> Events:
>   Type    Reason             Age   From                   Message
> ----    ------             ----  ----                   -------
>   Normal  ScalingReplicaSet  13m   deployment-controller  Scaled up replica set nginx-deployment-84b896cf65 to 2

```
kubectl get replicaset
```



```
kubectl describe replicaset nginx-deployment-**

kubectl get pod

kubectl describe pod nginx-deployment-**
```

#### yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx
    labels:
        app: nginx
spec: 
    replicas: 1
    selector:
        matchLabels:
            app: nginx
    template:
        metadata:
            labels:
                app: nginx
        spec:
            containers:
            - name: nginx
              image: nginx
              ports:
              - containerPort: 80
```

```
kubectl apply -f nginx.yml

kubectl describe deployment nginx
```

> Name:                   nginx
> Namespace:              default
> CreationTimestamp:      Sat, 24 Apr 2021 02:33:33 -0400
> Labels:                 app=nginx
> Annotations:            deployment.kubernetes.io/revision: 1
> Selector:               app=nginx
> Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
> StrategyType:           RollingUpdate
> MinReadySeconds:        0
> RollingUpdateStrategy:  25% max unavailable, 25% max surge
> Pod Template:
>   Labels:  app=nginx
>   Containers:
>    nginx:
>     Image:        nginx
>     Port:         80/TCP
>     Host Port:    0/TCP
>     Environment:  <none>
>     Mounts:       <none>
>   Volumes:        <none>
> Conditions:
>   Type           Status  Reason
>
> ----           ------  ------
>   Available      True    MinimumReplicasAvailable
>   Progressing    True    NewReplicaSetAvailable
> OldReplicaSets:  <none>
> NewReplicaSet:   nginx-7848d4b86f (1/1 replicas created)
> Events:
>   Type    Reason             Age   From                   Message
> ----    ------             ----  ----                   -------
>   Normal  ScalingReplicaSet  17s   deployment-controller  Scaled up replica set nginx-7848d4b86f to 1

#### delete

```
kubectl delete deployment nginx-deployment
kubectl delete -f nginx.yml
```

#### **Deployment -> Replicaset -> Pod**



### DaemonSet

#### knowledge

```
kubectl explain daemonset
```



每个 Node 上最多只能运行一个副本

```
kubectl get daemonset --namespace=kube-system
```

> NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
> kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   4d8h

```
 kubectl get pod --namespace=kube-system -o wide
```

> NAME                                                      READY   STATUS    RESTARTS   AGE    IP                NODE                              NOMINATED NODE   READINESS GATES
> coredns-54d67798b7-cdknt                                  1/1     Running   7          4d8h   172.17.0.3        control-plane.minikube.internal   <none>           <none>
> etcd-control-plane.minikube.internal                      1/1     Running   8          4d8h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-apiserver-control-plane.minikube.internal            1/1     Running   8          4d8h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-controller-manager-control-plane.minikube.internal   1/1     Running   8          4d8h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-proxy-scwzq                                          1/1     Running   7          4d8h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> kube-scheduler-control-plane.minikube.internal            1/1     Running   8          4d8h   192.168.128.128   control-plane.minikube.internal   <none>           <none>
> storage-provisioner                                       1/1     Running   12         4d8h   192.168.128.128   control-plane.minikube.internal   <none>           <none>

```
kubectl edit daemonset kube-proxy --namespace=kube-system

kubectl explain daemonset

kubectl get deployment
kubectl edit deployment nginx
```

#### sample

##### node-exporter

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
    name: node-exporter-daemonset
spec:
    selector:
        matchLabels:
            app: prometheus
    template:
        metadata:
            labels:
                app: prometheus
        spec:
            hostNetwork: true
            containers:
            - name: node-exporter 
              image: prom/node-exporter
              imagePullPolicy: IfNotPresent
              command:
              - /bin/node_exporter
              - --path.procfs
              - /host/proc 
              - --path.sysfs
              - /host/sys 
              - --collector.filesystem.ignored-mount-points
              - ^/(sys|proc|dev|host|etc)($|/)
              volumeMounts:
              - name: proc 
                mountPath: /host/proc 
              - name: sys 
                mountPath: /host/sys 
              - name: root
                mountPath: /rootfs
            volumes:
            - name: proc 
              hostPath:
                path: /proc 
            - name: sys 
              hostPath:
                path: /sys 
            - name: root 
              hostPath: 
                path: /
```



```
kubectl apply -f node_exporter.yml
```

> daemonset.apps/node-exporter-daemonset created

```
kubectl get pod -o wide

kubectl describe pod node-exporter-daemonset-8grmb
kubectl logs node-exporter-daemonset-8grmb
```



### Job

#### knowledge

一次性任务，完成后容器就退出

#### 服务类容器： 

Deployment, ReplicaSet, DaemonSet

#### sample

```
kubectl explain job
```

```
apiVersion: batch/v1
kind: Job
metadata:
    name: myjob
spec:
    template:
        metadata:
            name: myjob
        spec:
            containers:
            - name: hello
              image: busybox
              command: ["echo", "hello k8s job"]
            restartPolicy: Never
```



```
kubectl apply -f myjob.yml
kubectl get job
kubectl get pod
```

> myjob-87fv5                      0/1     Completed   0          55s

```
kubectl logs myjob-87fv5
```

> hello k8s job

```
kubectl get cronjob
kubectl get job

```



#### 查看支持的 verisions

```
kubectl api-versions
```

> admissionregistration.k8s.io/v1
> admissionregistration.k8s.io/v1beta1
> apiextensions.k8s.io/v1
> apiextensions.k8s.io/v1beta1
> apiregistration.k8s.io/v1
> apiregistration.k8s.io/v1beta1
> apps/v1
> authentication.k8s.io/v1
> authentication.k8s.io/v1beta1
> authorization.k8s.io/v1
> authorization.k8s.io/v1beta1
> autoscaling/v1
> autoscaling/v2beta1
> autoscaling/v2beta2
> batch/v1
> batch/v1beta1
> certificates.k8s.io/v1
> certificates.k8s.io/v1beta1
> coordination.k8s.io/v1
> coordination.k8s.io/v1beta1
> discovery.k8s.io/v1beta1
> events.k8s.io/v1
> events.k8s.io/v1beta1
> extensions/v1beta1
> flowcontrol.apiserver.k8s.io/v1beta1
> networking.k8s.io/v1
> networking.k8s.io/v1beta1
> node.k8s.io/v1
> node.k8s.io/v1beta1
> policy/v1beta1
> rbac.authorization.k8s.io/v1
> rbac.authorization.k8s.io/v1beta1
> scheduling.k8s.io/v1
> scheduling.k8s.io/v1beta1
> storage.k8s.io/v1
> storage.k8s.io/v1beta1
> v1



## Service

### Service Configure

#### deployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels: 
        app: httpd
    spec:
      containers:
      - name: httpd
        image: httpd
        ports:
        - containerPort: 80
```



#### service

````
apiVersion: v1
kind: Service
metadata: 
  name: httpd-svc 
spec:
  selector: 
    app: httpd 
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 80
````



#### command

```
kubectl get service
kubectl describe service httpd-svc
```

> NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
> hello-minikube   NodePort    10.102.79.117   <none>        8080:32298/TCP   4d
> httpd-svc        ClusterIP   10.101.75.103   <none>        8080/TCP         11m
> kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP          4d10h
>
> 
>
> Name:              httpd-svc
> Namespace:         default
> Labels:            <none>
> Annotations:       <none>
> Selector:          app=httpd
> Type:              ClusterIP
> IP Families:       <none>
> IP:                10.101.75.103
> IPs:               10.101.75.103
> Port:              <unset>  8080/TCP
> TargetPort:        80/TCP
> ==Endpoints:         172.17.0.6:80,172.17.0.7:80,172.17.0.8:80==
> Session Affinity:  None
> Events:            <none>

```
curl 10.101.75.103:8080
```



### Cluster IP

虚拟 ip，由 iptables 规则管理

```
iptables-save | grep httpd-svc
```

> -A KUBE-SEP-NG4C2J2BPF22E7YY -s 172.17.0.7/32 -m comment --comment "default/httpd-svc" -j KUBE-MARK-MASQ
> -A KUBE-SEP-NG4C2J2BPF22E7YY -p tcp -m comment --comment "default/httpd-svc" -m tcp -j DNAT --to-destination 172.17.0.7:80
> -A KUBE-SEP-UBJEA3CQ2ZK4WQHT -s 172.17.0.6/32 -m comment --comment "default/httpd-svc" -j KUBE-MARK-MASQ
> -A KUBE-SEP-UBJEA3CQ2ZK4WQHT -p tcp -m comment --comment "default/httpd-svc" -m tcp -j DNAT --to-destination 172.17.0.6:80
> -A KUBE-SEP-VGWTYDHUGJCFZT74 -s 172.17.0.8/32 -m comment --comment "default/httpd-svc" -j KUBE-MARK-MASQ
> -A KUBE-SEP-VGWTYDHUGJCFZT74 -p tcp -m comment --comment "default/httpd-svc" -m tcp -j DNAT --to-destination 172.17.0.8:80
> -A KUBE-SERVICES ! -s 10.244.0.0/16 -d 10.101.75.103/32 -p tcp -m comment --comment "default/httpd-svc cluster IP" -m tcp --dport 8080 -j KUBE-MARK-MASQ
> -A KUBE-SERVICES -d 10.101.75.103/32 -p tcp -m comment --comment "default/httpd-svc cluster IP" -m tcp --dport 8080 -j KUBE-SVC-IYRDZZKXS5EOQ6Q6
> -A KUBE-SVC-IYRDZZKXS5EOQ6Q6 -m comment --comment "default/httpd-svc" -m statistic --mode random --probability 0.33333333349 -j KUBE-SEP-UBJEA3CQ2ZK4WQHT
> -A KUBE-SVC-IYRDZZKXS5EOQ6Q6 -m comment --comment "default/httpd-svc" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-NG4C2J2BPF22E7YY
> -A KUBE-SVC-IYRDZZKXS5EOQ6Q6 -m comment --comment "default/httpd-svc" -j KUBE-SEP-VGWTYDHUGJCFZT74

**iptables 将访问 service 的流量转发到后端 pod，使用类似轮询的负载均衡策略。**

**cluster 的每一个节点都配置了相同的 iptables 规则**



### DNS 访问

可通过 cluster ip 访问，也可以通过 dns 访问

```
kubectl get deployment --namespace=kube-system
kubectl get deployment --all-namespaces
```

> NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
> default                hello-minikube              1/1     1            1           4d1h
> default                httpd                       3/3     3            3           27m
> kube-system            coredns                     1/1     1            1           4d11h
> kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           4d10h
> kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           4d10h



```
kubectl run busybox --rm -ti --image=busybox /bin/sh

# wget httpd-svc.default:8080
# wget httpd-svc:8080
# nslookup httpd-svc
```

> Server:         10.96.0.10
> Address:        10.96.0.10:53
>
> ** server can't find httpd-svc.default.svc.cluster.local: NXDOMAIN

```
kubectl get namespace
```



### 外网访问

#### ClusterIP

cluster 内部 IP， cluster 内部节点和 pod 可访问

#### NodePort

通过 cluster 节点的静态端口对外提供服务，外部可通过  nodeip:nodeport 访问 service



#### LoadBalancer

cloud provider 特有的 load balancer 对外提供服务， GCP, AWS, Azur, etc.



#### samples

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels: 
        app: httpd
    spec:
      containers:
      - name: httpd
        image: httpd
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata: 
  name: httpd-svc 
spec:
  type: NodePort
  selector: 
    app: httpd
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 80        
```



```
kubectl get service
```

> NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
> hello-minikube   NodePort    10.102.79.117   <none>        8080:32298/TCP   4d1h
> httpd-svc        NodePort    10.101.75.103   <none>        8080:30439/TCP   43m
> kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP          4d11h

```
netstat -an|grep 30439
```

> tcp        0      0 0.0.0.0:30439           0.0.0.0:*               LISTEN

```
curl 192.168.128.128:30439
```



```
iptables-save | grep httpd-svc
```

> -A KUBE-NODEPORTS -p tcp -m comment --comment "default/httpd-svc" -m tcp --dport 30439 -j KUBE-MARK-MASQ
> -A KUBE-NODEPORTS -p tcp -m comment --comment "default/httpd-svc" -m tcp --dport 30439 -j KUBE-SVC-IYRDZZKXS5EOQ6Q6

指定端口：

```
apiVersion: v1
kind: Service
metadata: 
  name: httpd-svc 
spec:
  type: NodePort
  selector: 
    app: httpd
  ports:
  - protocol: TCP
    nodePort: 30000
    port: 8080
    targetPort: 80  
```



## Rolling Update

一次只更新一小部分副本，成功后再更新更多副本



### Roll Back

```
kubectl apply -f rollback-v1.yml --record
kubectl get dpeloyment httpd -o wide

kubectl rollout hhistory deployment httpd
```

> deployment.apps/httpd
> REVISION  CHANGE-CAUSE
> 1         kubectl apply --filename=rollback-v1.yml --record=true
> 2         kubectl apply --filename=rollback-v2.yml --record=true
> 3         kubectl apply --filename=rollback-v3.yml --record=true



```
kubectl rollout undo deployment httpd --to-revision=1
```

> deployment.apps/httpd
> REVISION  CHANGE-CAUSE
> 2         kubectl apply --filename=rollback-v2.yml --record=true
> 3         kubectl apply --filename=rollback-v3.yml --record=true
> 4         kubectl apply --filename=rollback-v1.yml --record=true



## Health Check

### default:

进程退出，返回非零结果 -> 重启容器

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: healthcheck
  name: healthcheck
spec:
  restartPolicy: OnFailure
  containers:
  - name: healthcheck
    image: busybox
    args:
    - /bin/sh
    - -c
    - sleep 10; exit 1
```

> NAME                             READY   STATUS    RESTARTS   AGE
> healthcheck                      0/1     Error     3          88s
> hello-minikube-8947b65df-sthwj   1/1     Running   3          4d1h



### Liveness 探测

自定义重启策略 -> 失败后重启容器

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness
spec:
  restartPolicy: OnFailure
  containers:
  - name: liveness
    image: busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat 
        - /tmp/healthy 
      initialDelaySeconds: 10
      periodSeconds: 5
```

>  Normal   Scheduled  88s                default-scheduler  Successfully assigned default/liveness to control-plane.minikube.internal
>   Normal   Pulled     74s                kubelet            Successfully pulled image "busybox" in 13.619124416s
>   Warning  Unhealthy  32s (x3 over 42s)  kubelet            Liveness probe failed: cat: can't open '/tmp/healthy': No such file or directory
>   Normal   Killing    32s                kubelet            Container liveness failed liveness probe, will be restarted



### Readiness

自定义何时将容器加入到 service 负载均衡池中，对外提供服务。

配置与 liveness 完全一样，只要换成 `readinessProbe`

探测失败后 -> 将容器设置为不可用，不接收 service 转发的请求

可与 liveness 同时使用



适用于滚动更新中，只有通过了 readiness 探测才会被添加到 service ，保证业务正常进行



## Data Management

### Volume

持久化保存容器的数据

本质上是一个目录

挂载到 pod， pod 中所有容器都可访问

提供多种 backend 的抽象： emptyDir, hostPath, GCE Persistent Disk, AWS Elastic Block Store, NFS, Ceph, etc.



#### emptyDir

host 上的一个空目录

对容器持久，对 Pod 不是。

pod 从节点删除时，volume 的内容会被删除，如果只是容器被销毁而 pod 还在，则 volume 不受影响

**emptyDir Volume 的生命周期与 pod 一致**

```
apiVersion: v1
kind: Pod
metadata:
  name: producer-consumer
spec:
  containers:
  - image: busybox
    name: producer
    volumeMounts:
    - mountPath: /producer_dir
      name: shared-volume
    args:
    - /bin/sh
    - -c
    - echo "hello world" > /producer_dir/hello; sleep 30000
  - image: busybox 
    name: consumer 
    volumeMounts:
    - mountPath: /consumer_dir
      name: shared-volume
    args:
    - /bin/sh
    - -c 
    - cat /consumer_dir/hello; sleep 30000
  volumes:
  - name: shared-volume
    emptyDir: {}
```

```
kubectl get pod
```

> NAME                             READY   STATUS    RESTARTS   AGE
> hello-minikube-8947b65df-sthwj   1/1     Running   4          4d4h
> producer-consumer                2/2     Running   0          5s

```
kubectl logs producer-consumer consumer
```

> hello world

```
docker container ls | grep busybox
```

> 7a5ebd032758   busybox                                                         "/bin/sh -c 'cat /co…"    4 minutes ago   Up 4 minutes             k8s_consumer_producer-consumer_default_c36420ad-326c-4104-8232-177ff58f3dde_0
> ed2229bd2a67   busybox                                                         "/bin/sh -c 'echo \"h…"   4 minutes ago   Up 4 minutes             k8s_producer_producer-consumer_default_c36420ad-326c-4104-8232-177ff58f3dde_0

```
docker inspect 7a5ebd032758 | grep Mounts -A 10
```

> "Mounts": [
>             {
>                 "Type": "bind",
>                 "Source": "/var/lib/kubelet/pods/c36420ad-326c-4104-8232-177ff58f3dde/volumes/kubernetes.io~empty-dir/shared-volume",
>                 "Destination": "/consumer_dir",
>                 "Mode": "Z",
>                 "RW": true,
>                 "Propagation": "rprivate"
>             },
>             {
>                 "Type": "bind",



>  "Mounts": [
>             {
>                 "Type": "bind",
>                 "Source": "/var/lib/kubelet/pods/c36420ad-326c-4104-8232-177ff58f3dde/volumes/kubernetes.io~empty-dir/shared-volume",
>                 "Destination": "/producer_dir",
>                 "Mode": "Z",
>                 "RW": true,
>                 "Propagation": "rprivate"
>             },

**适合临时共享存储空间的场景，不具备持久性**



#### hostPath

将 docker host 文件系统中已有的目录 mount 给 pod 的容器，实际上增加了 pod 与节点的耦合，限制了 pod 的使用。

适用于需要访问  kubenetes 或 docker 内部数据，如 配置文件 和 二进制库 的应用

如 kube-apiserver, kube-controller-manage

```
kubectl get pod --namespace=kube-system
kubectl edit pod kube-apiserver-control-plane.minikube.internal --namespace=kube-system
```

>  volumeMounts:
>     - mountPath: /etc/ssl/certs
>       name: ca-certs
>       readOnly: true
>         - mountPath: /etc/pki
>       name: etc-pki
>       readOnly: true
>             - mountPath: /var/lib/minikube/certs
>       name: k8s-certs
>       readOnly: true
>                 - mountPath: /usr/share/ca-certificates
>       name: usr-share-ca-certificates
>       readOnly: true

...

>  volumes:
>   - hostPath:
>       path: /etc/ssl/certs
>       type: DirectoryOrCreate
>     name: ca-certs
>   - hostPath:
>       path: /etc/pki
>       type: DirectoryOrCreate
>     name: etc-pki
>   - hostPath:
>       path: /var/lib/minikube/certs
>       type: DirectoryOrCreate
>     name: k8s-certs
>   - hostPath:
>       path: /usr/share/ca-certificates
>       type: DirectoryOrCreate
>     name: usr-share-ca-certificates

**pod 被销毁， hostPath 对应的目录还会被保留，但 host 崩溃，hostPath 也就无法访问了**



附：

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
    name: node-exporter-daemonset
spec:
    selector:
        matchLabels:
            app: prometheus
    template:
        metadata:
            labels:
                app: prometheus
        spec:
            hostNetwork: true
            containers:
            - name: node-exporter 
              image: prom/node-exporter
              imagePullPolicy: IfNotPresent
              command:
              - /bin/node_exporter
              - --path.procfs
              - /host/proc 
              - --path.sysfs
              - /host/sys 
              - --collector.filesystem.ignored-mount-points
              - ^/(sys|proc|dev|host|etc)($|/)
              volumeMounts:
              - name: proc 
                mountPath: /host/proc 
              - name: sys 
                mountPath: /host/sys 
              - name: root
                mountPath: /rootfs
            volumes:
            - name: proc 
              hostPath:
                path: /proc 
            - name: sys 
              hostPath:
                path: /sys 
            - name: root 
              hostPath: 
                path: /
```





#### 外部 Storage Provider

AWS, GCE, Azure 等公有云上，可使用 `云硬盘`

```
apiVersion: v1
kind: Pod
metadata:
  name: using-ebs
spec:
  containers:
  - image: busybox
    name: using-ebs
    volumeMounts:
    - mountPath: /test-ebs
      name: ebs-volume
  volumes:
  - name: ebs-volume
    awsElasticBlockStore:
      volumeID: <volume-id>
      fsType: ext4
```

也可用分布式存储，如 Ceph, GlusterFS, etc.



### PersistentVolume

#### PersistentVolume

PV - 外部存储系统中的一块存储空间，由管理员创建和维护

具有持久性，生命周期独立于 pod



#### PersistentVolumeClaim

Claim - 申请

PVC - 对 PV 的申请

通常由普通用户创建和维护

只需请求资源，不需要关心真正的空间从哪分配， 如何访问等底层细节

支持多种，如 AWS EBS, Ceph, NFS, etc.

#### sample - 静态供给（Static Provision）

##### pv

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mypv1
spec:
  capacity:
    storage: 1Gi
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: nfs
  nfs:
    path: /home/app/nfsfile
    server: 192.168.128.10
```



```
kubectl get pv
```

> NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
> mypv1   1Gi        RWO            Recycle          Available           nfs                     6s



##### pvc

```
apiVersion: v1
kind: PersistentVolumeClaim 
metadata:
  name: mypvc1
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests: 
      storage: 1Gi 
  storageClassName: nfs
```



```
kubectl get pvc
```

> NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
> mypvc1   Bound    mypv1    1Gi        RWO            nfs            4s

```
kubectl get pv
```

> NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   REASON   AGE
> mypv1   1Gi        RWO            Recycle          Bound    default/mypvc1   nfs                     4m23s



##### pod

```
apiVersion: v1
kind: Pod 
metadata:
  name: mypod1
spec:
  containers:
  - name: mypod1 
    image: busybox 
    args:
    - /bin/sh
    - -c 
    - sleep 30000 
    volumeMounts:
    - mountPath: "/mydata"
      name: mydata 
  volumes:
  - name: mydata 
    persistentVolumeClaim:
      claimName: mypvc1 
```

```
kubectl exec mypod1 touch /mydata/hello
kubectl exec mypod1 -- touch /mydata/hello2

ls /home/app/nfsfile
```

##### delete

```
kubectl delete pvc mypvc1
```

Recycle 策略下删除 pvc 会清空挂载目录。

pv `persistentVolumeReclaimPolicy: Recycle` 改成 `persistentVolumeReclaimPolicy: Retain` 后，删除 pvc 不会导致数据被删掉



#### sample - 动态供给（Dynamical Provision）





## Secret

保存敏感数据，如密码，密钥等

以密文保存，以 Volume 形式被 mount 到 Pod，也可以 环境变量 的形式使用

通过 命令行 或 yml 创建

