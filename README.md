# mqtt-proxy

#### 介绍
是一款基于RocketMQ的MQTT物联网消息组件，其具备集群化高可用、高可靠性、支持百万连接数、低延迟切兼容开源MQTT物联网协议；相比开源Mosquitto等MQTT消息队列，其具备集群高可用性的能力，支持qos=0/1/2等服务等级的消息质量；

#### 软件架构
●MQTT服务，封装了MQTT协议的实现。并与namesrv、Broker模块通信实现离线消息的管理，会将订阅关系、遗嘱消息、保留消息等持久化。


#### 安装教程
1. Clone
git clone https://gitee.com/openeuler/mqtt-proxy.git

2. Build the package
cd mqtt-distribution
mvn -Prelease-all -DskipTests clean install -U

3. Config
cd mqtt-distribution/target/
cd mqtt-distribution/conf

4. Start Process
nohup sh mqtt -c ../conf/mqtt.properties &

#### 使用说明

1.  通过配置logback_mqtt.xml来控制日志的输出路径

#### 参与贡献

1.  Fork 本仓库
2.  新建 Feat_xxx 分支
3.  提交代码
4.  新建 Pull Request


#### 特技

1.  使用 Readme\_XXX.md 来支持不同的语言，例如 Readme\_en.md, Readme\_zh.md
2.  Gitee 官方博客 [blog.gitee.com](https://blog.gitee.com)
3.  你可以 [https://gitee.com/explore](https://gitee.com/explore) 这个地址来了解 Gitee 上的优秀开源项目
4.  [GVP](https://gitee.com/gvp) 全称是 Gitee 最有价值开源项目，是综合评定出的优秀开源项目
5.  Gitee 官方提供的使用手册 [https://gitee.com/help](https://gitee.com/help)
6.  Gitee 封面人物是一档用来展示 Gitee 会员风采的栏目 [https://gitee.com/gitee-stars/](https://gitee.com/gitee-stars/)
