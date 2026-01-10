# SSL PROCESS FOR KAFKA SERVER

### CA Certified Authority
openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes

### Keystore | KAFKA BROKER
keytool -genkey -keystore kafka.broker.keystore.jks -validity 365 -storepass password -keypass password -dname "CN=172.26.25.56" -storetype pkcs12 -keyalg RSA

### CSR Certificate Signing Request | KAFKA BROKER
keytool -keystore kafka.broker.keystore.jks -certreq -file cert-file -storepass password -keypass password

### Cert signed | KAFKA BROKER
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -extfile san.cnf -extensions v3_req -passin pass:password 

### Import CA public and Cert signed | KAFKA BROKER
keytool -keystore kafka.broker.keystore.jks -alias CARoot -import -file ca-cert -storepass password -keypass password -noprompt
keytool -keystore kafka.broker.keystore.jks -import -file cert-signed -storepass password -keypass password -noprompt
 
### Trustore | KAFKA BROKER
keytool -keystore kafka.broker.truststore.jks -alias CARoot -import -file ca-cert -storepass password -keypass password -noprompt

# SSL PROCESS FOR KAFKA CLIENT

### Trustore | KAFKA CLIENT
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass password -keypass password -noprompt

### keystore | KAFKA CLIENT
keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass password -keypass password -dname "CN=172.26.25.56" -storetype pkcs12 -keyalg RSA

### CSR Certificate Signing Request  | KAFKA CLIENT
keytool -keystore kafka.client.keystore.jks -certreq -file cert-file-client -storepass password -keypass password

### Cert signed | KAFKA CLIENT
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file-client -out cert-signed-client -days 365 -CAcreateserial -extfile san-client.cnf -extensions v3_req -passin pass:password 

### Import CA public and Cert signed | KAFKA CLIENT
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass password -keypass password -noprompt
keytool -keystore kafka.client.keystore.jks -import -file cert-signed-client -storepass password -keypass password -noprompt

# COMANDS CONSUMER/PRODUCER SSL

### Consumer
sh bin/kafka-console-consumer.sh --topic topic2 --from-beginning --bootstrap-server 172.26.25.56:9093 --consumer.config config/client.properties

### Producer
sh bin/kafka-console-producer.sh --topic topic2 --bootstrap-server 172.26.25.56:9093 -producer.config config/client.properties

# COMMANDS FOR KAFKA SERVER | SSL

### Start zookeper
sudo bin/zookeeper-server-start.sh config/zookeeper.properties

### Start kafka server 
sudo bin/kafka-server-start.sh config/server.properties

# COMANDS CONSUMER/PRODUCER SASL

### Consumer
sh bin/kafka-console-consumer.sh --topic topic2 --from-beginning --bootstrap-server 172.26.25.56:9095 --consumer.config config/client_reader_kerberos.properties

### Producer
sh bin/kafka-console-producer.sh --topic topic2 --bootstrap-server 172.26.25.56:9095 -producer.config config/client_writer_kerberos.properties

# COMANDS FOR KAFKA SERVER | PLAINTEXT

### Consumer
sh bin/kafka-console-consumer.sh --topic mi-topic --from-beginning --bootstrap-server 172.26.25.56:9092

### Producer
sh bin/kafka-console-producer.sh --topic mi-topic --bootstrap-server 172.26.25.56:9092

### Describe topics 

sh bin/kafka-topics.sh --describe --topic mi-topic --bootstrap-server localhost:9092

### Describe group
sh bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group mi-grupo-spring

### Increase partitions 

sh bin/kafka-topics.sh --bootstrap-server localhost:9092 \
  --alter --topic mi-topic \
  --partitions 3

# ACL 

### Create ACL CONSUMER
bin/kafka-acls.sh \
  --bootstrap-server 172.26.25.56:9095 \
  --command-config config/admin_client_kerberos.properties \
  --add \
  --allow-principal "User:reader" \
  --operation Read \
  --operation Describe \
  --topic topic2

### Create ACL PRODUCER
bin/kafka-acls.sh \
  --bootstrap-server 172.26.25.56:9095 \
  --command-config config/admin_client_kerberos.properties \
  --add \
  --allow-principal "User:writer" \
  --operation Write \
  --operation Describe \
  --topic topic2
