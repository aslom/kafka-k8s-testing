# kafka-k8s-testing

Useful tools to test Apache Kafka in Kubernetes and Knatvie Eventing

## Basic connectivity testing

Basic connectivity - ping Kafka broker port using telnet

```
$ docker run -it aslom/telnet /usr/bin/telnet kafka01-prod02.messagehub.services.us-south.bluemix.net 9093
Trying 169.60.0.116...
Connected to kafka01-prod02.messagehub.services.us-south.bluemix.net.
Escape character is '^]'.
^CConnection closed by foreign host.
```

And test connectivity inside k8s in default namespace:

```
$ k apply -f telnet/telnet-default.yml
pod/telnet created
```

and run test connection to your broker:

```
 k exec -n default -ti telnet -- /usr/bin/telnet kafka01-prod02.messagehub.services.us-south.bluemix.net 9093
Trying 169.60.0.116...
Connected to kafka01-prod02.messagehub.services.us-south.bluemix.net.
Escape character is '^]'.
^CConnection closed by foreign host.
command terminated with exit code 1
```

If there is no broker or connection can not be established:

```
$ k exec -n default -ti telnet -- /usr/bin/telnet kafka01-prod02.messagehub.services.us-south.bluemix.net 9094
Trying 169.60.0.116...
telnet: Unable to connect to remote host: Connection refused
command terminated with exit code 1
```


## Kafka consumer and producer tests


Run local test


```
$ docker run -it aslom/kafka-cli  /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server 9.59.194.50:9092 --topic test --from-beginning
test83
^CProcessed a total of 1 messages
```

Run Kafka consumer and producer using kubectl exec

```
$ k apply -f kafka-cli/kafka-cli-default.yml
pod/kafka-cli created
```

And run testing


```


```


When failing

```
$ kubectl exec -n default -ti kafka-cli -- /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server 9.59.194.50:9092 --topic test --from-beginning
[2019-04-02 16:20:08,335] WARN [Consumer clientId=consumer-1, groupId=console-consumer-93167] Connection to node -1 (/9.59.194.50:9092) could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
^CProcessed a total of 0 messages
command terminated with exit code 130
```



Check

```
docker run -it kafka-cli /bin/bash
```

```
docker run -it kafka-cli  /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server host:9092 --topic test --from-beginning
```

Deploy in default names

```
kubectl apply -f kafka-cli-default.yml
```

And in knative-eventing namespace:

```
kubectl apply -f kafka-cli-knative-eventing.yml
```

Run condumer in default namespace:

```
kubectl exec -n default -ti kafka-cli -- /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server host:9092 --topic test --from-beginning
```

And if more testing needed:

```
kubectl exec -n default -ti kafka-cli -- /bin/bash
```

### Testing with secured Kafka broker

Create config file with properties for example:

```
$ cat my-kafka-config.properties
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="T7f3xuwnKG0Q5V71" password="Xwup9VRp9...";
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
ssl.protocol=TLSv1.2
ssl.enabled.protocols=TLSv1.2
ssl.endpoint.identification.algorithm=HTTPS
```

Create and use secret mounted as file /opt/config/my-kafka-config.properties


```
k create secret generic kafka-secret --from-file=my-kafka-config.properties

k describe secret kafka-secret

k get secret kafka-secret -o yaml

k apply -f kafka-cli/kafka-cli-default-with-secret.yml

k describe pod kafka-cli

k exec -it kafka-cli -- /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka01-prod02.messagehub.services.us-south.bluemix.net:9093  --consumer.config /opt/config/my-kafka-config.properties --topic my-topic --from-beginning


k exec -it kafka-cli -- /opt/kafka/bin/kafka-console-producer.sh --broker-list kafka01-prod02.messagehub.services.us-south.bluemix.net:9093 --producer.config /opt/config/my-kafka-config.properties --topic my-topic
```

Example:

```
 k exec -it kafka-cli -- /opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server kafka01-prod02.messagehub.services.us-south.bluemix.net:9093  --consumer.config /opt/config/oimessage-kafka-config.properties --topic my-topic --from-beginning
{"msg": "This is a test A3!"}
te
t2
t3
^CProcessed a total of 4 messages
```
