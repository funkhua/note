## k8s



## Kubernetes 入门



### kubernetes基础知识

- 在kubernetes中，Service(服务)是分布式集群架构的核心，一个Service对象拥有如下关键特征。
  - 拥有一个唯一指定的名字
  - 拥有一个虚拟IP(Cluster IP、Service IP 或 VIP) 和端口号
  - 能够提供某种远程服务的能力
  - 被映射到了提供这种能力的一组容器应用上
- 扩展service需要创建Repliction Controller，RC包括：
  - 目标Pod的定义
  - 目标Pod需要运行的副本数量(Replicas)
  - 要监控的目标Pod的标签(Label)

- 以上信息暂时不懂，不要紧，记住就好。



### K8S 架构设计

- Kubernetes集群： Master组件 和 代理节点
- ![architecture](/Users/roger/Downloads/architecture.png)
- K8S的核心组件
  - etcd 保存整个集群的状态
  - apiserver 提供了资源操作的唯一入口，并提供认证、授权、访问控制、api注册和发现等机制
  - controller manager 负责维护集群的状态，比如故障监测、自动扩展、滚动更新等
  - schedule 负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上
  - kubelet 负责维护容器的生命周期，同时也负责管理Pods以及容器、镜像、Volume(CVI)和网络(CNI)的管理
  - container runtime 负责镜像管理以及Pod和容器的真正运行(CRI)
  - Kube-proxy 负责为Service提供cluster内部的服务发现和负载均衡
- 除了核心组件，还有一些推荐Add-one：
  - kube-dns 负载为整个集群提供DNS服务
  - Ingress Controller 为服务提供外网入口
  - Heapster 提供资源监控
  - Dashboard 提供GUI
  - Federation 提供跨可用区的集群
  - Fluentd-elasticsearch 提供集群日志采集、存储、查询

- 分层架构
  - 核心层：Kubernetes最核心的功能，对外提供API构建高层的应用，对内提供插件式应用执行环境
  - 应用层：部署(无状态应用、有状态应用、批处理任务、集群应用等)和路由(服务发现、DNS解析等)
  - 管理层：系统度量(如基础设施、容器和网络的度量)，自动化(如自动扩展、动态Provision)
  - 接口层：kubectl命令行工具、客户端SDK以及集群联邦
  - 生态系统：在接口层之上的庞大容器集群管理调度的生态系统，可以划分为两个范畴
    - kubernetes外部：日志、监控、配置管理、CI、CD、Workflow、Faas、OTS应用、ChatOps等
    - kubernetes内部：CRI、CNI、CVI、镜像仓库、Cloud Provider、集群自身的配置和管理等

### 开始搞

- 环境准备
  - 系统 |  CentOS 7.4 

- ``` config
  1. 关闭CentOS的自带防火墙服务
  	# systemctl disable firewalld
  	# systemctl stop firewalld
  
  2. 安装etcd和kubernetes软件 (会自动安装Docker软件)
  	# yum install -y etcd kubernetes
  	
  3. 修改两个配置文件，其他保持默认
  	- Docker配置文件 /etc/sysconfig/docker, 其中OPTIONS的内容设置为：
  	  OPTIONS='--selinux-enabled=false --insecure-registry gcr.io'
  	- Kubernetes apiserver配置文件为/etc/kubernetes/apiserver，把--admission_control参数中的ServiceAccount删除。
  	
  4. 按顺序启动所有服务：
  	#systemctl start etcd
      #systemctl start docker
      #systemctl start kube-apiserver
      #systemctl start kube-controller-manager
      #systemctl start kube-scheduler
      #systemctl start kubelet
      #systemctl start kube-proxy
      
  注：示例中的Docker镜像下载地址为https://hub.docker.com/u/kubeguide/
  ```

- 

  

  

  





