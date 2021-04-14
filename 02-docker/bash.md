
## 一 、帮助命令
```shell
docker version                                    #查看版本!
docker  info                                      #详细的信息 包括镜像和容器个数
docker  --help

https://docs.docker.com/get-started/resources/    #docker文档    
https://hub.docker.com/                           #docker仓库
https://www.kuangstudy.com/course?cid=1           #狂神官网
````


## 二、镜像命令
 ```shell
 docker images                                   #显示下载的镜像。
 docker images -a                                #显示下载的镜像。
 docker images -q                                #只显示显示镜像id
 docker images -aq                               #显示所有的并且显示镜像id
 docker search mysql                             #从仓库中搜索镜像,镜像默认最新版本
 docker search mysql:5.7                         #制定版本查询
 docker search mysql --filter=STARS=3000         #过滤收藏超过3000的镜像
 
  docker pull   mysql                             #拉取mysql实例,docker采用分层下载方式
 docker pull   mysql:5.7                        #拉取mysql实例,docker采用分层下载方式
 docker rmi    镜像id                          #删除镜像
 docker rmi -f  镜像id                         #强制删除镜像
 docker rmi -f  $(docker images -aq)            #批量强制删除所有镜像
 ```


## 三、容器命令

```shell
docker run  image 

        #参数说明
        --name="Name"          #容器名称
        -d                     #后台运行          
        -it                    #使用交互方式进入容器
        -p
            -p ip:主机端口:容器端口
            -p 主机端口:容器端口(常用)
            -p 容器端口
            容器端口
        
        -P                     #随机制定端口            
        
docker run   -it cetnos /bin/bash     #启动并且进入容器(curl+p+q 退出不停止容器)
docker  ps                            #查看运行的容器列表
docker  ps  -a                        #查看运行的+历史运行的容器
docker  ps  -n=1                      #显示最近创建的几个容器
docker  ps  -aq                       #显示所有容器的编号

docker  rm    容器id                  #删除制定容器
docker  rm -f  容器id                 #强制删除制定容器
docker  rm -f  $(docker ps -aq)      #强制删除所有容器
docker ps -aq |xargs docker rm -f    #强制删除所有容器

docker start      容器id              #启动
docker restart    容器id              #重启
docker stop       容器id              #停止
docker  kill      容器id              #杀掉

```


## 四、其他命令

```shell
docker run -d centos          #后台运行容器

        #问题docekr ps 发现centos 停止了。
        #常见坑,docker容器使用后台运行,就必须有要一个前台进程,docker 发现没应用,就自动停止了。
        #nginx,容器启动后,发现自己没有提供服务,就会立刻停止,就是没有程序了。
        
docker run -d centos /bin/bash -c "while true;do echo a;sleep 1;done"   #运行容器并且执行内容。
docker logs -ft  容器id                                    #以时间戳的形式显示容器的日志
docker top       容器id                                    #查看容器内的进程信息
docker inspect   容器id                                   #查看镜像的元数据信息    

docker exec -it     容器id  bash                          #进入当前正在运行的容器,新开一个shell方式
docker attach  容器id  bash                               #进入当前正在运行的容器,是当前shell方式

docker  cp  容器id:路径  目标主机路径                       #在本地执行：从容器内拷贝到本地服务器 从里面拷贝出来

docker  volume ls                                       #查询docker卷信息
docker  volume  inspect jumingnginx                     #查询卷的详细信息

docker  commit -m="提交的描述信息" -a="作者" 容器id  目标镜像名:[TAG]      #修改了已有的容器后,在生成一个镜像。

#最后有. 是一个路径,这个路径作用： 就是 告诉docker 你的程序包  在什么路径下。
 docker build -f ./DockerFile -t diytomcat:1.0 /usr/local/src/        # 就是 告诉docker 你的程序包  在什么路径下。
docker build -f dockerfile文件路径  -t 镜像名:TAG .                     #自己定义dockerfile 从无到有生成镜像
docker  history ed0236714d27                                          #查看构建dockerfile步骤历史

docker login -u xuchaoyang  -p XXXXX       #登录docker hub
docker logout                                  #退出


docker tag 本地镜像名:版本 定义作者/本地镜像名:版本   <------------一定要打标签为作者名称不能随便定义
docker push xuchaoyang/diytomcat:1.0         

docker save 本地镜像名称 -o /tmp/centos.tar                     #将本地镜像生成文件进行保存。
docker load -i /tmp/centos.tar                                #将服务器的镜像文件加载到images仓库里。


docker network  ls                                            #查看网络列表
    bridge:     #桥接模式(默认),01 <--02--> 03 02等于大桥。
     none:     #不配置网络
     host:     #和宿主机共享网络
  container：  #容器内网络连通(用的少 局限很大)                                     
               
docker network rm 122421e88586                                #删除网络
docker network  create --driver bridge --subnet 192.168.0.0/16  --gateway 192.168.0.1 wonet   #创建网络
docker network  inspect redis                                 #查看reids名的网络详细信息
 
#将docker0网络的tomcat01容器和wonet进行连通,这样就可以实现跨网络容器之间进行连通了。[相当于一个容器两个ip地址]
docker network  connect wonet tomcat01  



练习
#部署tomcat
docker run -it  --rm  tomcat:9.0                        #测试方式启动容器,容器停止后就删除。

#部署nginx
docker run -d --name nginx01 -p 88888:80 nginx          #启动nginx容器


#部署es+kibana

#下载镜像启动容器并且-e限制内存大小
docker run -d --name elasticsearch  -p 9200:9200 -p 9300:9300 \
     -e  "discovery.type=single-node"  -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.12.0 
docker stats elasticsearch                             #查询docker里占用内存和cpu情况


可视化
#portainer (测试玩玩就行)
#它是一个图形化界面管理工具,提供一个后台界面版提供我们操作。
docker run -d -p 8088:9000 \
    --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer

#Rancher (CI/DC用)

```


## 五、联合文件系统

```shell
#镜像和镜像之间是公用底层的内容。
#这个中层级之间的系统叫 unionFS
#bootfs  引导系统加载 引导系统 加载内核
#rootfs  他就是小虚拟机环境
#镜像层   是无法改变的,只读的。
#容器层   是可以添加修改的内容,然后在创建为一个新的镜像。
docker pull redis                
    Using default tag: latest
    latest: Pulling from library/redis
    f7ec5a41d630:  Already exists <---------代表这一层已经下载其他镜像的时候下载过了,在公用这个资源比如系统
    a36224ca8bbd: Pull complete 
    7630ad34dcb2: Pull complete 
    dd0ea236b03b: Pull complete 
    ed6ed4f2f5a6: Pull complete 
    8788804112c6: Pull complete 
    Digest: sha256:08e282682a708eb7f51b473516be222fff0251cdee5ef8f99f4441a795c335b6
    Status: Downloaded newer image for redis:latest
    docker.io/library/redis:latest
    
    
 docker inspect redis  #查询联合文件系统那6层的地方。
      "Layers": [
                "sha256:7e718b9c0c8c2e6420fe9c4d1d551088e314fe923dce4b2caf75891d82fb227d",
                "sha256:89ce1a07a7e4574d724ea605b4877f8a73542cf6abd3c8cbbd2668d911fa5353",
                "sha256:9eef6e3cc2937e452b2325b227ca28120a70481be25404ed9aad27fa81219fd0",
                "sha256:c6e23529840f1d7025d61f4e41781d16fff2c0a2639484e9c0a2e516ed98c23a",
                "sha256:0c895bca4020c183c3f093cf920d29460ac5a25b7e48210d9f0655b1e115b7af",
                "sha256:fa723b6cb1b660d6c745a09892783a552657b9dba07f52974cb88365281feb19"
            ]   



commit镜像
docker commit 提交容器为一个新的副本
docker  commit -m="提交的描述信息" -a="作者" 容器id  目标镜像名:[TAG]

#新创建一个自己的镜像,操作类似git

#默认的tomcat镜像webapp下没有文件
docker run  -d -it -p 8080:8080 tomcat
docker exec -it 9868bc8dad38  bash
cp -r /usr/local/tomcat/webapps.dist/*  /usr/local/tomcat/webapps  

#创建自己的镜像
docker commit -a="xcy" -m="modify webapps dir" 9868bc8dad38  tomcatxcy:1.0

#查询镜像列表
docker images  -a      
REPOSITORY            TAG              IMAGE ID       CREATED          SIZE
tomcatxcy             1.0              d9714ec5941c   10 seconds ago   672MB
tomcat                latest           bd431ca8553c   28 hours ago     667MB

```


## 六、容器数据卷

```shell
数据卷是什么？
    #目录的挂载,将容器内的目录挂载到liunx上面。
    #目的：数据的持久化和同步操作,容器间也是可以数据共享的。
   

     
      
使用逻辑卷,双向绑定
docker run -d -it -v 主机目录:容器目录 -p主机端口:容器端口
docker run -d -it -v /tmp/test1/:/tmp/  --name centos01 centos:centos7.6.1810  bash

docker inspect centos01
    "Mounts": [
            {
                "Type": "bind",
                "Source": "/host_mnt/private/tmp/test1",     <----本机的
                "Destination": "/tmp",                       <-----容器内
                "Mode": "",           
                "RW": true,
                "Propagation": "rprivate"
            }
        ]
        
  
  
        


练习
#安装mysql

#启动mysql初始化密码
docker run -d  -it -p 3310:3306 -v /home/mysql/conf/:/etc/my.cnf -v /home/mysql/data/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret-pw mysql:5.7 --name mysql01
docker inspect mysql01
 




具名和匿名挂载
-v 容器内路径                         #匿名挂载
-v 卷名:容器内路径                     #具名挂载 
-v /宿主机路径:容器内路径               #制定路径挂载



docker  volume --help
docker  volume ls                      #查询所有卷的情况
   

1. 匿名挂载
   
    docker run -d -P --name nginx01 -v /etc/nginx nginx   
    docker  volume ls                      #查询所有卷的情况,这种就是匿名卷,因为在-v的时候没有写外部的名字
    DRIVER    VOLUME NAME
    local     57fb5060c0a3eb013ea73aeb5270b1769a5d87b8ace782f4fff46e15c90c2a1d
    local     e62cdae423fda4bfeeb374259e5164f0ad5777fa669561145b94ca2ee7298928
    
    
2. 具名挂载,启个名字，jumingnginx不是路径。
  
    docker run -d -P --name nginx02 -v jumingnginx:/etc/nginx nginx 
    
    #查询所有卷的情况,通过-v 卷名:容器内名字
    docker  volume ls                     
        DRIVER    VOLUME NAME
        local     jumingnginx
    
    #查询卷的详细信息    
    docker  volume  inspect jumingnginx
                [
            {
                "CreatedAt": "2021-04-12T07:32:15Z",
                "Driver": "local",
                "Labels": null,
                "Mountpoint": "/var/lib/docker/volumes/xxx/_data",
                "Name": "jumingnginx",
                "Options": null,
                "Scope": "local"
            }
        ]  
        
 3. 通过-v 容器路径： ro,rw,改变读写权限 
   #一但设置容器,挂载出来的目录就不能改变权限了。  
 docker run -d -P --name nginx02 -v jumingnginx:/etc/nginx:ro nginx 
 docker run -d -P --name nginx02 -v jumingnginx:/etc/nginx:rw nginx
 
 


Docker系统目录
ll /var/lib/docker/
builder
buildkit
containers    #容器目录
image         #镜像目录
network       #网络配置
overlay2
plugins       #插件目录
runtimes
swarm
tmp
trust
volumes       #卷挂载目录




多个容器 数据卷共享使用
#--volumes-from  #子容器继承父容器的目录,双向同步,其实就是使用的同一个目录
#容器之间配置信息的传递,数据卷容器生命周期一直持续到没有容器使用为止。
#但是持久化本地,这个时候本地的数据是不会删除的。

docker run -d -it --name centos-master xcycentos:1.0
docker run -d -it --name centos-slave01 --volumes-from centos-master  xcycentos:1.0


#启动连个mysql实例共享同一个目录数据,读写分离来用
docker run -d  -it -p 3310:3306 -v /etc/my.cnf -v/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret-pw mysql:5.7 --name mysql01
docker run -d  -it -p 3311:3306  --volumes-from mysql01  -e MYSQL_ROOT_PASSWORD=secret-pw mysql:5.7 --name mysql02 

```


## 七、DockerFile初识

```shell
#dockerfile 就是来构建docker 镜像文件,就是一段命令脚本,通过脚本可以生成镜像,每一步就是一层。


简单使用
vim dockerfile01 
    FROM centos                       <-----镜像采用centos
    VOLUME ["volume01","volume02"]    <-----创建两个目录 叫volume01和volume02
    CMD echo "-----end----"           <-----完成后打印输出end
    CMD /bin/bash                      <-----使用bash
    
#build生成命令 
docker build  -f ./dockerfile01  -t xcycentos:1.0 .

#查询镜像列表
docker images  -a                                  
REPOSITORY      TAG              IMAGE ID       CREATED        SIZE
xcycentos       1.0              5a91d78accaa   4 months ago   209MB


完整流程 
编写一个dockerfile  #构建dockerfile 
docker build       #构建一个镜像
docker run         #运行镜像
docker push        #发布镜像到镜像仓库


基础知识:
    1 每个关键字都是大写的
    2 顺序执行
    3 #表示注释
    4 每个内容都是一层一层提交
    



DockerFile的指令
那么当我们使用 docker build 命令来构建镜像时，这个构建过程其实是在 Docker引擎 中完成的，而不是在本机环境。
那么如果在 Dockerfile 中使用了一些 COPY 等指令来操作文件，如何让 Docker引擎 获取到这些文件呢？
这里就有了一个 镜像构建上下文 的概念，当构建的时候，由用户指定构建镜像的上下文路径，而 docker build 会将这个路径下所有的文件都打包上传给 Docker 引擎，引擎内将这些内容展开后，就能获取到所有指定上下文中的文件了。
比如说 dockerfile 中的 COPY ./package.json /project，其实拷贝的并不是本机目录下的 package.json 文件，而是 docker引擎 中展开的构建上下文中的文件，所以如果拷贝的文件超出了构建上下文的范围，Docker引擎 是找不到那些文件的。
所以 docker build 最后的 . 号，其实是在指定镜像构建过程中的上下文环境的目录。

#忽略大小写,但是建议大写
#docker hub中99%镜像都是从这个基础镜像过来的FROM scratch 然后配置需要的软件和配置来往下面构建。

FROM                     #基础镜像,一切从这里开始
MAINTAINER               #镜像是谁写的,姓名+邮箱
RUN                      #镜像构建的时候需要运行的命令
ADD                      #步骤,tomcat镜像,这个tomcat的压缩包 添加内容,add 会自动解压，
WORKDIR                  #镜像的工作目录
VOLUME                   #挂载的目录
EXPOSE                   #对外暴露的端口
RUN                      #运行
CMD                      #制定容器启动时要运行的命令,只有最后一个会生效,可被替换。
ENTRYPOINT               #制定容器启动时要运行的命令,可追加命令,命令都会执行。
ONBUILD                 #当构建一个被继承 dockerfile这个时候就会运行ONBUILD的指令,触发指令。
COPY                    #类似ADD 将我们文件拷贝到镜像中,但是复制不会解压
ENV                     #构建的时候设置环境变量



########编写案例1      创建一个自己的简单的centos 

1. 编写一个自己的dockerfile 

vim  mydockerfile
    FROM centos
    MAINTAINER xcy<920966977@qq.com>
    ENV MYPATH /tmp/test1            <------定义工作目录
    WORKDIR $MYPATH
    RUN yum -y install unzip 
    RUN yum -y install vim 
    EXPOSE 80
    CMD echo $MYPATH
    CMD echo "----end----"
    CMD /bin/bash
    
2 通过文件构建镜像
docker build -f dockerfile文件路径  -t 镜像名:TAG .
docker build -f ./mydockerfile -t mycent0001:0.01 .
  
3 测试运行
docker run  -it  mycent0001:0.01   bash     
[root@b269f4a53dd1 test1]# pwd      <------一进来就是定义的目录工作目录
/tmp/test1  



########编写案例2  验证CMD和ENTRYPOINT的区别
#CMD              只有最后一个命令会生效
#ENTRYPOINT       会叠加执行

vim cmd01
    FROM centos
    CMD ["ls","-a"]
    
docker build -f ./cmd01 -t cmd01:0.1 .
docker run be8cf7de8763   <----------------运行后输出ls -a 内容
    bin
    dev
    etc
    home
    lib
    lib64
    lost+found
    media

  #想追加一个命令-l  ls -al
docker run cmd01:0.1  -l   <---------CMD的清理下,-l 替换了CMD ["ls","-a"] 命令,-l不是命令 所以报错
docker: Error response from daemon: OCI runtime create failed: container_linux.go:370: starting container process caused: exec: "-l": executable file not found in $PATH: unknown.



vim  en01
    FROM centos
    ENTRYPOINT ["ls","-a"]

docker build -f ./en01 -t en01:0.1 .
docker run be8cf7de8763  
    bin
    dev
    etc
    home
    lib
    lib64
    lost+found
    media

 docker run en01:0.1  -l   <---------------这里可以追加命令
    total 56
    drwxr-xr-x   1 root root 4096 Apr 13 03:15 .
    drwxr-xr-x   1 root root 4096 Apr 13 03:15 ..
    -rwxr-xr-x   1 root root    0 Apr 13 03:15 .dockerenv
    lrwxrwxrwx   1 root root    7 Nov  3 15:22 bin -> usr/bin
    drwxr-xr-x   5 root root  340 Apr 13 03:15 dev
 



########编写案例3  tomcat镜像
 1. 准备镜像文件,tomcat压缩包，jdk压缩包
2. 编写dockerfile文件
3  DockerFile 这个名字是官网命令,以后规范写这样命令,这样就不用加-f参数了。

jdk下载：    https://www.oracle.com/java/technologies/javase/javase7-archive-downloads.html#license-lightbox
tomcat下载： https://mirrors.bfsu.edu.cn/apache/tomcat/tomcat-7/v7.0.108/bin/apache-tomcat-7.0.108.tar.gz


#ls -l
-rw-r--r--@ 1 carl  staff   9.2M  4 13 11:23 apache-tomcat-7.0.108.tar.gz
-rw-r--r--@ 1 carl  staff   146M  4 13 11:23 jdk-7u80-linux-x64.tar.gz
-rw-r--r--  1 carl  admin     0B  4 13 11:28 readme.txt

vim DockerFile
    FROM centos
    MAINTAINER xcy<920966977@qq.com>
    COPY readme.txt  /usr/local/readme.txt
    ADD  jdk-7u80-linux-x64.tar.gz   /usr/local/      <-------add命令会自动解压
    ADD apache-tomcat-7.0.108.tar.gz /usr/local/
    RUN yum -y install vim 
    ENV MYPATH /usr/local
    WORKDIR $MYPATH
    ENV JAVA_HOME /usr/local/jdk1.7.0_80
    ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tool.jar
    ENV CATALINA_HOME /usr/local/apache-tomcat-7.0.108
    ENV CATALINA_BASE /usr/local/apache-tomcat-7.0.108
    ENV PATH $PATH:$JAVA_HOME:/bin:$CATALINA_HOME:/lib:$CATALINA_HOME:/bin
    EXPOSE 8080
    CMD  /usr/local/apache-tomcat-7.0.108/bin/startup.sh && tail -f /usr/local/apache-tomcat-7.0.108/logs/catalina.out
    
    
docker build -f ./DockerFile -t diytomcat:1.0 . 
docker run -d  -p 8008:8080 diytomcat:1.0
docker  exec -it  38c74d17c037  bash

[root@38c74d17c037 local]#ls -l
    total 52
    drwxr-xr-x 1 root root 4096 Apr 13 04:29 apache-tomcat-7.0.108
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 bin
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 etc
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 games
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 include
    drwxr-xr-x 8   10  143 4096 Apr 11  2015 jdk1.7.0_80
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 lib
    drwxr-xr-x 3 root root 4096 Dec  4 17:37 lib64
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 libexec
    -rw-r--r-- 1 root root    0 Apr 13 03:28 readme.txt
    drwxr-xr-x 2 root root 4096 Nov  3 15:22 sbin
    drwxr-xr-x 5 root root 4096 Dec  4 17:37 share





bash-3.2$ docker build -f ./DockerFile -t diytomcat:1.0 /usr/local/src/ 
.pass.txt                     DockerFile                    jdk-7u80-linux-x64.tar.gz     mydata/                       
.tmp                          apache-tomcat-7.0.108.tar.gz  login_vm.sh                   readme.txt                    

bash-3.2$ docker build -f ./DockerFile -t diytomcat:1.0 /usr/local/src/ 
[+] Building 8.6s (11/11) FINISHED                                                                                                                                       
 => [internal] load build definition from DockerFile                                                                                                                0.0s
 => => transferring dockerfile: 680B                                                                                                                                0.0s
 => [internal] load .dockerignore                                                                                                                                   0.0s
 => => transferring context: 2B                                                                                                                                     0.0s
 => [internal] load metadata for docker.io/library/centos:latest                                                                                                    2.9s
 => [1/6] FROM docker.io/library/centos@sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1                                                     0.0s
 => [internal] load build context                                                                                                                                   5.5s
 => => transferring context: 163.23MB                                                                                                                               5.5s
 => CACHED [2/6] COPY readme.txt  /usr/local/readme.txt                                                                                                             0.0s
 => CACHED [3/6] ADD  jdk-7u80-linux-x64.tar.gz   /usr/local/                                                                                                       0.0s
 => CACHED [4/6] ADD apache-tomcat-7.0.108.tar.gz /usr/local/                                                                                                       0.0s
 => CACHED [5/6] RUN yum -y install vim                                                                                                                             0.0s
 => CACHED [6/6] WORKDIR /usr/local                                                                                                                                 0.0s
 => exporting to image                                                                                                                                              0.0s
 => => exporting layers                                                                                                                                             0.0s
 => => writing image sha256:d441b1809932a34c28373257f45be2f3c17096a43a3dbf3b41f8e015c8d7d8ee                                                                        0.0s
 => => naming to docker.io/library/diytomcat:1.0  
    
```

## 八、镜像发布到Docker hub

```shell

1. 注册一个账号 https://hub.docker.com 
2. 确定账号可以登录  
3.进行打tag 注意docker hub上传需要打自己账号名的tag才能上传。 
4 在服务器上提交自己的镜像


#先登录自己的账户
docker login -u xuchaoyang  -p XXXXXX
    WARNING! Using --password via the CLI is insecure. Use --password-stdin.
    Login Succeeded

docker tag diytomcat:1.0 xuchaoyang/diytomcat:1.0    <-------- 需要打自己账号名的tag才能上传
docker push xuchaoyang/diytomcat:1.0
        
```

## 九、镜像发布到阿里云镜像服务
```shell

1 登录阿里云
2 找到容器镜像服务
3 创建一个命名空间
4 创建容器镜像

docker login --username=阿里云账号 registry.cn-beijing.aliyuncs.com
docker tag diytomcat:1.0 阿里云账号/diytomcat:1.0
docker push 阿里云账号/diytomcat:1.0 

```

## 十、 Docker网络原理

```shell 

理解：docker0
ip addr 
    lo  网路
    ech0 网络
    docker0 网络

docker run -d -P --name tomcat01 tomcat 
docker exec -it tomcat01 ip addr     <--------发现容器启动的时候会得到一个eth0@if57 ip 地址 docker分配的
        1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
    2: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
        link/ipip 0.0.0.0 brd 0.0.0.0
    3: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN group default qlen 1000
        link/tunnel6 :: brd ::
    56: eth0@if57: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
           valid_lft forever preferred_lft forever

本地服务器进行ping
ping 172.17.0.2  是可以ping通的。

我们每启动一个docker容器,docker就会给docker容器分配一个ip，
我们只要安装了docker,就会有一个网卡docker0 桥接模式,使用的技术是evth-pair技术。
启动一个容器以后,在外面物理服务器上也会多出一个和容器内相同的网卡
我们发现这些容器带来的网卡都是一对一对的,evth-pair 就是一对的虚拟设备接口,他们都是成对出现的,
一段连着协议,一段彼此相连,正因为有这个特性,,evth-pair充当一个桥梁。

结论 tomcat1和tomcat2是公用的一个路由,docker0
所有的容器不指定网络的情况下,都是docker0路由的。
docker会给我们的容器分配一个可用ip。
docker中所有网络都是虚拟的。
         
                                   
           
           


--link
功能的作用  前端配置数据库地址后 如果mysql容器重启了ip会改变,怎么样可以配置一个名字 ip怎么变也不用修改应用

docker run -d -P --name tomcat01 tomcat 
docker run -d -P --name tomcat02 tomcat 
docker exec -it tomcat02 ping tomcat01   <-------------默认是ping不同的,通过容器名称
    ping: tomcat01: Name or service not known
    
解决办法：
docker run -d -P --name tomcat03 --link tomcat02 tomcat 
docker exec -it tomcat03 ping tomcat02        <-------------可以通了。  
    PING tomcat02 (172.17.0.3) 56(84) bytes of data.
    64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.314 ms

原理：
其实--link 就是在容器中配置了/etc/hosts内容.
登录tomcat03 查看/etc/hosts
docker exec -it tomcat03  cat /etc/hosts  
127.0.0.1       localhost
172.17.0.3      tomcat02 66269415cebe
172.17.0.4      757d07e42d4c


我们现在玩docker 已经不建议使用 --link 了 
docker0问题： 他不支持容器名链接访问！




自定义网络 容器互联
#我们直接启动的命令 --net bridge 而这个就是我们的docker0 --net bridge 是默认参数
docker run -d -P --name tomcat01 --net bridge tomcat


#####自定义网络创建容器
1 创建网络
docker network  create --driver bridge --subnet 192.168.0.0/16  --gateway 192.168.0.1 wonet
docker network ls  
 NETWORK ID     NAME      DRIVER    SCOPE
 b8a6e15e8384   wonet     bridge    local
    
2 运行容器
docker run -d -P --name tomcat-net01 --net wonet  tomcat 
docker run -d -P --name tomcat-net02 --net wonet  tomcat  
docker exec -it tomcat-net01 ping tomcat-net02   <------- 可以ping通
docker exec -it tomcat-net02 ping tomcat-net01   <------- 可以ping通



####链接一个容器到网络 实现跨网络 容器进行连通

#将docker0网络的tomcat01容器和wonet进行连通,这样就可以实现跨网络容器之间进行连通了,[相当于一个容器两个ip地址]
docker network  connect wonet tomcat01

     
```

## 十一、redis 集群部署
```shell

#创建网卡
docker network create redis --subnet 172.38.0.0/16

#通过脚本创建留个reids的配置
for port in $(seq 1 6);\
do \
mkdir -p /usr/local/src/mydata/redis/node-${port}/conf
touch /usr/local/src/mydata/redis/node-${port}/conf/redis.conf
cat <<EOF > /usr/local/src/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done


#启动容器
for port in $(seq 1 6);\
do \
docker run -p 637${port}:6379 -p 1637${port}:16379 \
 --name redis-${port} \
 -v /usr/local/src/mydata/redis/node-${port}/data:/data \
 -v /usr/local/src/mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf;\
 done

    Unable to find image 'redis:5.0.9-alpine3.11' locally
    5.0.9-alpine3.11: Pulling from library/redis
    cbdbe7a5bc2a: Pull complete 
    dc0373118a0d: Pull complete 
    cfd369fe6256: Pull complete 
    3e45770272d9: Pull complete 
    558de8ea3153: Pull complete 
    a2c652551612: Pull complete 
    Digest: sha256:83a3af36d5e57f2901b4783c313720e5fa3ecf0424ba86ad9775e06a9a5e35d0
    Status: Downloaded newer image for redis:5.0.9-alpine3.11
    c8ef3e05a8405dcf878f0873e72728fae6a9e62bbdb4b8920f2c792802fb62ff
    bb24031f10d82addc968c9199ce7e6fa8482c4481b36ea6f7027115ed18e358f
    90483bbd1d25a7778e13b8ad8c92fe002e818d09c4a67552900ea01677e79936
    ec7d931473287d0ebb74fd6c4724c8251d5286f589a15125ff95e34ebd93c54a
    98ad86bf3f3ed7593ff5cf9cdf8d847e2996e1c7f0f173d57c56b4a016c2b290
    f319acb9292bce139e611d6d764da159aa25306dd2e6d869f1a5bfdb76949b49
    (base)  carl@P_CHAOYXU-MB0  /tmp  docker ps                                
    CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS                                              NAMES
    f319acb9292b   redis:5.0.9-alpine3.11   "docker-entrypoint.s…"   4 seconds ago   Up 2 seconds   0.0.0.0:6376->6379/tcp, 0.0.0.0:16376->16379/tcp   redis-6
    98ad86bf3f3e   redis:5.0.9-alpine3.11   "docker-entrypoint.s…"   5 seconds ago   Up 3 seconds   0.0.0.0:6375->6379/tcp, 0.0.0.0:16375->16379/tcp   redis-5
    ec7d93147328   redis:5.0.9-alpine3.11   "docker-entrypoint.s…"   6 seconds ago   Up 4 seconds   0.0.0.0:6374->6379/tcp, 0.0.0.0:16374->16379/tcp   redis-4
    90483bbd1d25   redis:5.0.9-alpine3.11   "docker-entrypoint.s…"   7 seconds ago   Up 5 seconds   0.0.0.0:6373->6379/tcp, 0.0.0.0:16373->16379/tcp   redis-3
    bb24031f10d8   redis:5.0.9-alpine3.11   "docker-entrypoint.s…"   7 seconds ago   Up 6 seconds   0.0.0.0:6372->6379/tcp, 0.0.0.0:16372->16379/tcp   redis-2
    c8ef3e05a840   redis:5.0.9-alpine3.11   "docker-entrypoint.s…"   8 seconds ago   Up 7 seconds   0.0.0.0:6371->6379/tcp, 0.0.0.0:16371->16379/tcp   redis-1
    

#进入redis 容器创建redis集群
docker exec -it  redis-1 sh
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 \
--cluster-replicas 1

redis-cli  -c 
cluster info
cluster nodes


SpringBoot 微服务大包docker 镜像
1 创建一个简单可运行的web框架程序输出helloword 
 案例是用java写的 名为 app.jar
 
2 创建dockerfile 
vim DockerFile
    FROM java:8
    COPY *.jar /app.jar
    CMD ["--server.port=8080"] 
    EXPOSE 8080
    ENTRYPOINT ["java","-jar","/app.jar"]
    
3 生成镜像
docker build -f ./DockerFile -t app:1.0 .
docker run -d --name app01  app:1.0 

Docker Compose 
#yaml文件


Docker Swarm
#容器编排

CI/CD之 jenkins    
 
```





