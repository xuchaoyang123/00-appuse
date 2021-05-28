<!--
 * @Author: your name
 * @Date: 2021-05-09 21:29:49
 * @LastEditTime: 2021-05-28 12:53:28
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /01-good/use/08. Kubernetes/003.kubeadm方式部署k8s.md
-->

> ## kubeadm 方式安装 k8s

#

### 1.平台规划

```bash

  节点规划:
        #master 192.168.44.141
        #node1  192.168.44.142
        #mode2  192.168.44.143

 测试环境最小要求
            master
                    #2核 4G 20G
            node
                    #4核 8G 40G


```

### 2.服务器初始化

```bash


#1.[所有节点] 关闭防火墙
	systemctl stop firewalld
	systemctl disable firewalld

#2.[所有节点] 关闭seliunx
	sed -i 's/enforcing/disabled/' /etc/seliunx/config #永久
	setenforce 0 #临时

#3.[所有节点] 关闭swap
	swapoff -a  #临时
	sed -ri 's/.*swap.*/#&/' /etc/fstab #永久

#4.[所有节点] 根据规划设置主机名
	hostnamectl set-hostname master
	hostnamectl set-hostname node1
	hostnamectl set-hostname node2

#5.[master节点] 在master添加hosts
	cat >> /etc/hosts <<EOF
	192.168.44.141 master
	192.168.44.142 node1
	192.168.44.143 node2
	EOF

#6.[所有节点] 将桥接的ipv4流量传递到iptables的链
	cat > /etc/sysctl.d/k8s.conf <<EOF
	net.bridge.bridge-nf-call-ip6tables=1
	net.bridge.bridge-nf-call-iptables=1
	EOF
	sysctl --system  #生效


#7.[所有节点] 时间同步
	yum install ntpdate -y
	ntpdate time.windos.com

```

### 3.安装 docker

```bash
   #kubernetes默认CRI(容器运行时)为docker,因此先安装docker.

#1.[所有节点] yum配置
        yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo

#2.[所有节点] 安装
        yum install docker-ce docker-ce-cli containerd.io -y               安装docker ce 社区版


#3.[所有节点] 配置国内镜像加速
        sudo mkdir -p /etc/docker                                           #更换国内镜像站
        sudo tee /etc/docker/daemon.json <<-'EOF'
        {
        "registry-mirrors": ["https://slfru11m.mirror.aliyuncs.com"]
        }
        EOF

#4.[所有节点] 启动服务
        sudo systemctl daemon-reload
        sudo systemctl restart docker

#5.[所有节点] 测试
        docker version                                                      #查看版本!
        docker  info                                                        #详细的信息 包括镜像和容器个数
        docker run  hello-word                                             #测试


```
