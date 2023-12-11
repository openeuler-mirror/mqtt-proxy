# mqtt-proxy

#### 介绍
是一款基于RocketMQ的MQTT物联网消息组件，其具备集群化高可用、高可靠性、支持百万连接数、低延迟切兼容开源MQTT物联网协议；相比开源Mosquitto等MQTT消息队列，其具备集群高可用性的能力，支持qos=0/1/2等服务等级的消息质量；

#### 软件架构
●MQTT服务，封装了MQTT协议的实现。并与namesrv、Broker模块通信实现离线消息的管理，会将订阅关系、遗嘱消息、保留消息等持久化。
![mqtt-proxy](docs/pictures/mqtt-proxy.png)

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
使用mqtt客户端进行测试  

、、、
<dependency>
    <groupId>org.eclipse.paho</groupId>
    <artifactId>org.eclipse.paho.client.mqttv3</artifactId>
    <version>1.2.5</version>
</dependency>
、、、  

public class MqttClientDemo {
    public static void main(String[] args) throws Exception {
        String endPoint = "XXXXX";

        String clientId = "XXXXXX";
        /**
         * Mqtt 消息的一级 topic
         */
        final String parentTopic = "XXXXX";
        /**
         * Mqtt 支持子级 topic，用来做自定义的过滤，此处为示意，可以填写任何字符串
         * 需要注意的是，完整的 topic 长度不得超过65535个字符。
         */
        final String mqttTopic = parentTopic + "/" + "testMqtt";
        /**
         * QoS参数代表传输质量，可选0，1，2，根据实际需求合理设置
         */
        final int qosLevel = 0;
        ConnectionOptionWrapper connectionOptionWrapper = new ConnectionOptionWrapper(accessKey, secretKey, clientId);

        final MqttClient mqttClient = new MqttClient("tcp://" + endPoint + ":1883", clientId, memoryPersistence);

        /**
         * 客户端设置好发送超时时间，防止无限阻塞
         */
        mqttClient.setTimeToWait(5000);
        mqttClient.setCallback(new MqttCallbackExtended() {
            public void connectComplete(boolean reconnect, String serverURI) {
                /**
                 * 客户端连接成功后就需要尽快订阅需要的 topic
                 */
                System.out.println("connect success");

                try {
                    final String topicFilter[] = {mqttTopic};
                    final int[] qos = {qosLevel};
                    mqttClient.subscribe(topicFilter, qos);
                } catch (MqttException e) {
                    e.printStackTrace();
                }

            }

            public void connectionLost(Throwable throwable) {
                throwable.printStackTrace();
            }

            public void messageArrived(String s, MqttMessage mqttMessage) throws Exception {
                /**
                 * 消费消息的回调接口，需要确保该接口不抛异常，该接口运行返回即代表消息消费成功。
                 * 消费消息需要保证在规定时间内完成，如果消费耗时超过服务端约定的超时时间，对于可靠传输的模式，服务端可能会重试推送，业务需要做好幂等去重处理。
                 */
                System.out.println(
                    "receive msg from topic " + s + " , body is " + new String(mqttMessage.getPayload()));
            }

            public void deliveryComplete(IMqttDeliveryToken iMqttDeliveryToken) {
                System.out.println("send msg succeed topic is : " + iMqttDeliveryToken.getTopics()[0]);
            }
        });

        mqttClient.connect(connectionOptionWrapper.getMqttConnectOptions());
        for (int i = 0; i < 10; i++) {
            MqttMessage message = new MqttMessage("hello mqtt pub sub msg".getBytes());
            message.setQos(qosLevel);
            /**
             *  发送普通消息时，topic 必须和接收方订阅的 topic 一致，或者符合通配符匹配规则
             */
            mqttClient.publish(mqttTopic, message);
        }
        /**
         *  客户端取消订阅的topic
         */
        mqttClient.unsubscribe(mqttTopic);

        Thread.sleep(Long.MAX_VALUE);
    }
}
、、、
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
