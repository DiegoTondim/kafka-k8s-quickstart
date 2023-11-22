```sh

# install kafka on pinot namespace
helm install -n pinot-quickstart kafka oci://registry-1.docker.io/bitnamicharts/kafka

# connect to kafka broker
kubectl -n kafka exec my-cluster-kafka-0 -ti -- sh

# create topics
./kafka-topics.sh --bootstrap-server localhost:9092 --topic flights-realtime --create --partitions 1 --replication-factor 1

./kafka-topics.sh --bootstrap-server localhost:9092 --topic flights-realtime-avro --create --partitions 1 --replication-factor 1
```