# docker compose file.

```
services:
  kafka:
    image: apache/kafka:4.1.0
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_NODE_ID: 1
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093

      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092,PLAINTEXT_INTERNAL://0.0.0.0:29092,CONTROLLER://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092,PLAINTEXT_INTERNAL://kafka:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,PLAINTEXT_INTERNAL:PLAINTEXT
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER

      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1

      KAFKA_LOG_DIRS: /var/lib/kafka/data
      CLUSTER_ID: MkU3OEVBNTcwNTJENDM2Qk

    volumes:
      - kafka_data:/var/lib/kafka/data

  kafdrop:
    image: obsidiandynamics/kafdrop:latest
    container_name: kafdrop
    ports:
      - "9000:9000"
    environment:
      KAFKA_BROKERCONNECT: kafka:29092
    depends_on:
      - kafka

volumes:
  kafka_data:
```

save above one as docker-compose.yml

**1. Start the docker with compose file**
```docker-compose up -d```

It creates two containers.
**1. Kafka and 2. Kafdrop**

------------------------------------
# Hands-on: Create Your First Topic
Now let’s create a topic! Think of this as creating a new newspaper section.

**Step 1: Enter the Kafka Container**
 ```docker exec -it kafka bash```

**Step 2: Create a Topic**
Inside the container, run:

Move to bin directory :/opt/kafka/bin and check all commands available

Then command to create the topic

```
./kafka-topics.sh --create \
  --bootstrap-server localhost:9092 \
  --replication-factor 1 \
  --partitions 3 \
  --topic my-first-topic
```
What this means:

--create: We're creating a new topic
--bootstrap-server localhost:9092: Where Kafka is running
--replication-factor 1: How many copies of data (1 = no replication, fine for local dev)
--partitions 3: Split the topic into 3 partitions (like 3 lanes on a highway)
--topic my-first-topic: The name of our topic
You should see: Created topic my-first-topic.


**Step 3: Verify Your Topic**
Let’s list all topics to confirm it was created:

```./kafka-topics.sh --list --bootstrap-server localhost:9092```
You should see my-first-topic in the list.


**Step 4: Describe Your Topic (Optional but Useful)**
See detailed information about your topic:

```./kafka-topics.sh --describe \
  --bootstrap-server localhost:9092 \
  --topic my-first-topic```

This shows:
Partition count (3)
Replication factor (1)
Which broker is the leader for each partition

**Step 5:**
Exit the container:

```exit```



