# Kafka broker server

### How to start server?

- 1. Generate a Cluster UUID

KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"

- 2. Format Log Directories

bin/kafka-storage.sh format --standalone -t $KAFKA_CLUSTER_ID -c config/server.properties

- 3. Export KAFKA_OPTS varible in your linux enviroment

export KAFKA_OPTS="-Djava.security.auth.login.config=$HOME/documents/kafka-clusters/kafka-kraf-ssl-sasl/config/kafka_server_jaas.conf"

- 4. Start the Kafka Server

bin/kafka-server-start.sh config/server.properties
