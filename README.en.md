# mqtt-proxy

#### Description
It is an MQTT IoT messaging component based on RocketMQ, which has clustering, high availability, high reliability, support for millions of connections, low latency, and compatibility with the open-source MQTT IoT protocol; Compared to open-source MQTT message queues such as Mosquito, it has the ability of high cluster availability and supports message quality at service levels such as QoS=0/1/2

#### Software Architecture
MQTT service encapsulates the implementation of the MQTT protocol. And communicate with namesrv and Broker modules to manage offline messages, persisting subscription relationships, testament messages, retention messages, etc.
![mqtt-proxy](docs/pictures/mqtt-proxy.png)

#### Installation
1. Clone git clone https://gitee.com/openeuler/mqtt-proxy.git

2. Build the package cd mqtt-distribution mvn -Prelease-all -DskipTests clean install -U

3. Config cd mqtt-distribution/target/ cd mqtt-distribution/conf

4. Start Process nohup sh mqtt -c ../conf/mqtt.properties &

#### Instructions

1. add mqtt-paho client dependency in your project
```
        <dependency>
            <groupId>org.eclipse.paho</groupId>
            <artifactId>org.eclipse.paho.client.mqttv3</artifactId>
            <version>1.2.5</version>
        </dependency>
```
2. use mqtt-client demo test
```java
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
```

#### Contribution

1.  Fork the repository
2.  Create Feat_xxx branch
3.  Commit your code
4.  Create Pull Request


#### Gitee Feature

1.  You can use Readme\_XXX.md to support different languages, such as Readme\_en.md, Readme\_zh.md
2.  Gitee blog [blog.gitee.com](https://blog.gitee.com)
3.  Explore open source project [https://gitee.com/explore](https://gitee.com/explore)
4.  The most valuable open source project [GVP](https://gitee.com/gvp)
5.  The manual of Gitee [https://gitee.com/help](https://gitee.com/help)
6.  The most popular members  [https://gitee.com/gitee-stars/](https://gitee.com/gitee-stars/)
