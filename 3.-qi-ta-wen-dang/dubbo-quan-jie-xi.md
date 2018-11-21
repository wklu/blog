# Dubbo

## 环境预备

**zk安装**： [https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz](https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz) 解压后复制 zoo\_sample.cfg 为 zoo.cfg bin/zkServer.sh start 启动 bin/zkServer.sh status 查看状态 zookeeper.out文件记录日志

**tomcat安装**： [http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-7/v7.0.91/bin/apache-tomcat-7.0.91.tar.gz](http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-7/v7.0.91/bin/apache-tomcat-7.0.91.tar.gz)

**dubbo-admin安装**（[https://pan.baidu.com/s/1OjuyfgdIgR49rwnrzC-J5g](https://pan.baidu.com/s/1OjuyfgdIgR49rwnrzC-J5g) 密码：ax7t） [https://github.com/apache/incubator-dubbo/archive/dubbo-2.6.0.zip](https://github.com/apache/incubator-dubbo/archive/dubbo-2.6.0.zip) 打包：解压后进入项目incubator-dubbo-dubbo-2.6.0\dubbo-admin，mvn clean package 将生成的war包放入tomcat的webapp下

## dubbo要解决的问题

1. rpc调用需要定制，额外的工作量
2. 分布式服务中，服务动辄几十上百，相互之间的调用错综复杂，相互依赖严重
3. 对集群性的服务，需要负载策略
4. 对集群性的服务，能动态扩展节点



