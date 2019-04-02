# kafka-k8s-testing

Useful tools to test Apache Kafka in Kubernetes and Knatvie Eventing

## Basic connectivity testing

Basic connectivity - ping Kafka broker port using telnet

```
$ docker run -it telnet /usr/bin/telnet kafka01-prod02.messagehub.services.us-south.bluemix.net 9093
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

Run Kafka consumer and producer using kubectl exec



k apply -f kafka-cli-knative-eventing.yml