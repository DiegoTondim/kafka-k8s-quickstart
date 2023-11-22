## Introduction to Kafka on Docker :whale:

```sh
# start services
docker-compose up -d

# get name brocker
docker-compose ps

# access container broker
CONTAINER_NAME=broker
docker exec -it $CONTAINER_NAME bash

# declare variables on container
BOOTSTRAP_SERVER=localhost:9092
TOPIC=topico
GROUP=grupo

# create topic with kafka cli
kafka-topics --create --bootstrap-server $BOOTSTRAP_SERVER \
--replication-factor 1 \
--partitions 3 \
--topic $TOPIC

kafka-topics --create --bootstrap-server $BOOTSTRAP_SERVER \
--replication-factor 1 \
--partitions 3 \
--topic bookings_topic

# list topics after created
kafka-topics --list --bootstrap-server $BOOTSTRAP_SERVER

# configs about topic
kafka-topics --bootstrap-server $BOOTSTRAP_SERVER \
--describe \
--topic $TOPIC

# create producer
kafka-console-producer --broker-list $BOOTSTRAP_SERVER \
--topic $TOPIC

# producer with key
kafka-console-producer --broker-list $BOOTSTRAP_SERVER \
--topic $TOPIC --property parse.key=true --property key.separator=* value.serializer=custom.class.serialization.JsonSerializer

# bookings
12345*{"id": 12345, "passenger": "Diego Tondim", "amount": 56.00, "status": "Created", "balanceDue": 56.00, "checkinStatus": null}
12345*{"id": 12345, "passenger": "Diego Tondim", "amount": 56.00, "status": "Confirmed", "balanceDue": 0.00, "checkinStatus": null}
12345*{"id": 12345, "passenger": "Diego Tondim", "amount": 56.00, "status": "Confirmed", "balanceDue": 0.00, "checkinStatus": "CheckedIn"}

5544*{"id": 5544, "passenger": "Diego Tondim", "amount": 56.00, "status": "Created", "balanceDue": 56.00, "checkinStatus": null}
5544*{"id": 5544, "passenger": "Diego Tondim", "amount": 56.00, "status": "Confirmed", "balanceDue": 0.00, "checkinStatus": null}
5544*{"id": 5544, "passenger": "Diego Tondim", "amount": 56.00, "status": "Confirmed", "balanceDue": 0.00, "checkinStatus": "CheckedIn"}

99999999*{"id": 99999999, "passenger": "Diego Tondim", "amount": 56.00, "status": "Created", "balanceDue": 56.00, "checkinStatus": null}
99999999*{"id": 99999999, "passenger": "Diego Tondim", "amount": 56.00, "status": "Confirmed", "balanceDue": 0.00, "checkinStatus": null}
99999999*{"id": 99999999, "passenger": "Diego Tondim", "amount": 56.00, "status": "Confirmed", "balanceDue": 0.00, "checkinStatus": "CheckedIn"}

# send message to topic
abc
def
ghi
jkl
mno
pqr
stu
vwx
yza

CONTAINER_NAME=broker
docker exec -it $CONTAINER_NAME bash

BOOTSTRAP_SERVER=localhost:9092
TOPIC=topico
GROUP=grupo


kafka-console-consumer --bootstrap-server $BOOTSTRAP_SERVER \
--topic $TOPIC \
--from-beginning

# read keys + value in json
kafka-console-consumer --bootstrap-server $BOOTSTRAP_SERVER --topic $TOPIC --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true value.serializer=custom.class.serialization.JsonSerializer --group $GROUP

# read topic and partitions
kafka-console-consumer --bootstrap-server $BOOTSTRAP_SERVER \
--topic $TOPIC \
--group $GROUP

# read topic from beginning
kafka-console-consumer --bootstrap-server $BOOTSTRAP_SERVER \
--topic $TOPIC \
--from-beginning

# ksql

CREATE STREAM bookings (
  id STRING KEY,
  status STRING,
  passenger STRING,
  checkinStatus STRING,
  amount DOUBLE,
  balanceDue DOUBLE ) 
  WITH (
  KAFKA_TOPIC='topico',
  VALUE_FORMAT='JSON');

CREATE TABLE IF NOT EXISTS bookings_table WITH (
  KAFKA_TOPIC='bookings_topic',
  VALUE_FORMAT='JSON'
) AS SELECT
  id,
  LATEST_BY_OFFSET(status) AS status_latest,
  LATEST_BY_OFFSET(checkinStatus) AS checkinStatus_latest
FROM bookings
GROUP BY id
EMIT CHANGES;

# stop services
docker-compose down
```