# Kafka broker server

### How to start server?

- Generate a Cluster UUID

$ KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"

- Format Log Directories

$ bin/kafka-storage.sh format --standalone -t $KAFKA_CLUSTER_ID -c config/server.properties

- Start the Kafka Server

$ bin/kafka-server-start.sh config/server.properties