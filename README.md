# docker-kafka

Kafka docker compose stack using [Bitnami Kafka images](https://github.com/bitnami/bitnami-docker-kafka).

- Kafka data is persisted into `./kafka` that bind in  docker compose volume mount.

- Kafka broker is available to all peer containers through hostname (i.e. docker compose service name) `kafka`. 

- Therefore, an example producer and consumer config in your app might be as follows:

  ```
  vi config.yml
  
    kafka_producer:
      bootstrap.servers: kafka:9092
      key.serializer: org.apache.kafka.common.serialization.StringSerializer
      value.serializer: org.apache.kafka.common.serialization.StringSerializer
      acks: 1
      retries: 3
      linger.ms: 1
    
    kafka_consumer:
      bootstrap.servers: kafka:9092
      key.deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value.deserializer: org.apache.kafka.common.serialization.StringDeserializer
      group.id: "test"
      enable.auto.commit: false
      auto.offset.reset: 'earliest'
  ```

### Access from host server machine

- To access Kafka broker from host, first append `kafka` hostname to localhost loopback like so:

  ```
  vi /etc/hosts
    127.0.0.1  localhost  kafka
  ```

- Then do like so:

  ```
  wget http://mirror.intergrid.com.au/apache/kafka/2.0.0/kafka_2.12-2.0.0.tgz
  tar xzf kafka_2.12-2.0.0.tgz
  
  Term1:
  bin/kafka-console-producer.sh --broker-list kafka:9092 --topic test
  
  
  Term2:
  bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic test --from-beginning
  ```

### Access from remote client through SSH tunnel

- Similarly, modify `hosts` file of your remote client machine to add `kafka` hostname to localhost loopback

  ```
  vi /etc/hosts
    127.0.0.1  localhost  kafka
  ```

- Then, punch the tunnel like so:

  ```
  ssh -L kafka:2181:kafka:2181 -L kafka:9092:kafka:9092 ubuntu@kafka.domain.com
  ```

