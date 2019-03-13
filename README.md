# kubernetes-kafka-cluster

## Requirements


- `go get github.com/AlexsJones/vortex`

- Also requires Zookeeper https://github.com/AlexsJones/kubernetes-zookeeper-cluster.git

## Resources

- https://cwiki.apache.org/confluence/display/KAFKA/Replication+tools


## Kafka manager UI

- Port forward the kafka-manager
`kubectl port-forward kafka-manager-686665c975-ck99c :9000`

## Example

_from the pod_

```
unset JMX_PORT; kafka-topics.sh --create --zookeeper zk-cs.zk.svc.cluster.local:2181 --replication-factor 3 --partitions 1 --topic test

unset JMX_PORT; kafka-topics.sh --zookeeper zk-cs.zk.svc.cluster.local:2181 --list

unset JMX_PORT; kafka-console-producer.sh --broker-list zk-hs.zk.svc.cluster.local:7004 --topic test

unset JMX_PORT; kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list localhost:7004 --topic test --time -1 --offsets 1 | awk -F ':' '{sum += $3} END {print sum}'
```

## Drop messages

```
unset JMX_PORT; kafka-topics.sh --zookeeper zk-cs.zk.svc.cluster.local:2181 --alter --topic $QUEUENAME --config retention.ms=1000
```

## Delete topic

```
./kafka-run-class.sh kafka.admin.TopicCommand --delete --topic test --zookeeper zk-cs.zk.svc.cluster.local:2181
```

## Testing

`kubectl run curl --image=radial/busyboxplus:curl -i --tty` useful for debugging inter node connections

`kubectl create -f test/pod.yaml` for load testing the cluster (delete to shutdown) _defaults to three node config_

