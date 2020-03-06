```text
Kafka��һ���ֲ�ʽ����-������Ϣϵͳ��һ��ǿ��Ķ���,���Դ������������,
��ʹ���ܹ�����Ϣ��һ���˵㴫�ݵ���һ���˵�;
1.Kafka�ļ�����������:
    Topics(����): �����ض�������Ϣ����Ϊ����;���ݴ洢��������;
    Partition(����): ÿ����������ж������;
    Partition offset(����ƫ��): ������ÿ����¼��Ψһ���б�ʶ;
    Replicas of partition(��������): �����ı���,�Ӳ���ȡ��д������;
    Brokers(������): kafka��Ⱥ��ÿ�������Ϊbroker;
    Producers(������): ���͸�һ������Kafka�������Ϣ�ķ�����;
    Consumers(������): ����һ����������,����broker��ȡ�ѷ�������Ϣ��ʹ��;
    Leader(�쵼��): ����������������ж�ȡ��д��Ľڵ�;ÿ����������һ���������䵱Leader;
    Follower(׷����): ͬ��Leader��partition��Ϣ;
    Consumer Group(��������): Topic��Ϣ���䵽��������,��������������䵽��������ʵ��;
    (ÿ���������ֻ�ܰ�һ��������,ÿ�������߿������Ѷ������)
2.Kafka��װʹ��:
    Kafka���ص�ַ:http://kafka.apache.org/downloads,ѡ��Binary downloads����,Ȼ���ѹ����;
    Kafka�������ļ�λ��configĿ¼��(����kafka��Zookeeper�������ļ�),��server.properties,
    ��broker.id��ֵ�޸�Ϊ1,ÿ��broker��id����������ΪInteger����,�Ҳ����ظ�;
    [1]����Zookeeper:
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ�������ű�: 
            bin\windows\zookeeper-server-start.bat config\zookeeper.properties
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,�Ժ�̨���̵ķ�ʽִ�������ű�:
            bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
    [2]����Kafka:
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ�������ű�:
            bin\windows\kafka-server-start.bat config\server.properties
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,ִ�������ű�:
            bin/kafka-server-start.sh config/server.properties
        �����������д�ӡstarted����Ϣ,˵���������;
    [3]����Topic:
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ�д���Topic�ű�:
            bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 
            --replication-factor 1 --partitions 1 --topic test
            (����һ��Topic��ZK(ָ��ZK�ĵ�ַ),��������Ϊ1,������Ϊ1,Topic������Ϊtest)
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,ִ�д���Topic�ű�:
            bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 
            --partitions 1 --topic test
    [4]�鿴Kafka���Topic�б�:
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ��Topic�ű�:
            bin\windows\kafka-topics.bat --list --zookeeper localhost:2181
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,ִ��Topic�ű�:
            bin/kafka-topics.sh --list --zookeeper localhost:2181
    [5]�鿴ĳ��Topic�ľ�����Ϣ: (��:test)
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ��Topic�ű�:
            bin\windows\kafka-topics.bat --describe --zookeeper localhost:2181 --topic test
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,ִ��Topic�ű�:
            bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test
    [6]����Producers:
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ��producer�ű�:
            bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
            (9092Ϊ�����ߵ�Ĭ�϶˿ں�,���������ߺ�,����test Topic�﷢������)
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,ִ��producer�ű�:
            bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
    [7]����Consumers:
        (1)Windows��,��cmd���л���Kafka��Ŀ¼,ִ��consumer�ű�:
            bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 
            --topic test --from-beginning
            (from-beginning��ʾ��ͷ��ʼ��ȡ����)
        (2)Linux��,���ն��������л���Kafka��Ŀ¼,ִ��consumer�ű�:
            bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 
            --topic test --from-beginning
3.Spring Boot����Kafaka:
    [1]����web������kafka����:
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
    [2]����������:
        (1)ͨ��������,���������߹�����kafkaģ��:
            @Configuration
            public class KafkaProducerConfig {
                @Value("${spring.kafka.bootstrap-servers}")
                private String bootstrapServers;
                @Bean
                public ProducerFactory<String, String> producerFactory() {
                    Map<String, Object> configProps = new HashMap<>();
                    configProps.put(
                            ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
                            bootstrapServers);
                    configProps.put(
                            //key�����л�����,String����
                            ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
                            StringSerializer.class);
                    configProps.put(
                            //value�����л�����,String����
                            ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
                            StringSerializer.class);
                    return new DefaultKafkaProducerFactory<>(configProps);
                }
                //������˷�����Ϣ�ı�ݷ���
                @Bean
                public KafkaTemplate<String, String> kafkaTemplate() {
                    return new KafkaTemplate<>(producerFactory());
                }
            }
        (2)�����ļ�application.yml�����������ߵĵ�ַ:
            spring:
                kafka:
                    bootstrap-servers: localhost:9092
    [3]��д������Ϣ��controller:
        @RestController
        public class SendMessageController {
            @Autowired
            private KafkaTemplate<String, String> kafkaTemplate;
            @GetMapping("send/{message}")
            public void send(@PathVariable String message) {
                // testΪTopic������,messageΪҪ���͵���Ϣ
                this.kafkaTemplate.send("test", message);
            }
        }
       send�������첽����,��ͨ���ص��ķ�ʽ��ȷ����Ϣ�Ƿ��ͳɹ�,����controller:
        @RestController
        public class SendMessageController {
            private Logger logger = LoggerFactory.getLogger(this.getClass());
            @Autowired
            private KafkaTemplate<String, String> kafkaTemplate;
            @GetMapping("send/{message}")
            public void send(@PathVariable String message) {
                ListenableFuture<SendResult<String, String>> future = 
                    this.kafkaTemplate.send("test", message);
                future.addCallback(new ListenableFutureCallback<SendResult<String,String>>(){
                    @Override
                    public void onSuccess(SendResult<String, String> result) {
                        logger.info("�ɹ�������Ϣ��{}��offset=[{}]", message, 
                            result.getRecordMetadata().offset());
                    }
                    @Override
                    public void onFailure(Throwable ex) {
                        logger.error("��Ϣ��{} ����ʧ�ܣ�ԭ��{}", message, ex.getMessage());
                    }
                });
            }
        }
    [4]����������:
        (1)ͨ��������,���������߹����ͼ�����������:
            //����������Ҫ@EnableKafkaע�Ͳ�����Spring�й�Bean�ϼ��@KafkaListenerע��
            @EnableKafka    
            @Configuration
            public class KafkaConsumerConfig {
                @Value("${spring.kafka.bootstrap-servers}")
                private String bootstrapServers;
                @Value("${spring.kafka.consumer.group-id}")
                private String consumerGroupId;
                @Value("${spring.kafka.consumer.auto-offset-reset}")
                private String autoOffsetReset;
                @Bean
                public ConsumerFactory<String, String> consumerFactory() {
                    Map<String, Object> props = new HashMap<>();
                    props.put(
                            ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,
                            bootstrapServers);
                    props.put(
                            ConsumerConfig.GROUP_ID_CONFIG,
                            consumerGroupId);
                    props.put(
                            ConsumerConfig.AUTO_OFFSET_RESET_CONFIG,
                            autoOffsetReset);
                    props.put(
                            ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,
                            StringDeserializer.class);
                    props.put(
                            ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
                            StringDeserializer.class);
                    return new DefaultKafkaConsumerFactory<>(props);
                }
                @Bean
                public ConcurrentKafkaListenerContainerFactory<String, String> 
                    kafkaListenerContainerFactory() {
                    ConcurrentKafkaListenerContainerFactory<String, String> factory
                            = new ConcurrentKafkaListenerContainerFactory<>();
                    factory.setConsumerFactory(consumerFactory());
                    return factory;
                }
            }
        (2)��application.yml��������������ID����Ϣ��ȡ����:
            spring:
                kafka:
                    consumer:
                        group-id: test-consumer
                        auto-offset-reset: latest
            
            ��Ϣ��ȡ����,�����ĸ���ѡֵ:
                earliest:���������������ύ��offsetʱ,���ύ��offset��ʼ����;
                    ���ύ��offsetʱ,��ͷ��ʼ����;
                latest:���������������ύ��offsetʱ,���ύ��offset��ʼ����;
                    ���ύ��offsetʱ,�����²����ĸ÷����µ�����;
                none:topic���������������ύ��offsetʱ,��offset��ʼ����;
                    ֻҪ��һ���������������ύ��offset,���׳��쳣;
                exception:ֱ���׳��쳣;
    [5]��д��Ϣ��������:
        @Component
        public class KafkaMessageListener {
            private Logger logger = LoggerFactory.getLogger(this.getClass());
            // ָ���������������������
            @KafkaListener(topics = "test", groupId = "test-consumer")
            public void listen(String message) {
                logger.info("������Ϣ: {}", message);
            }
        }
4.@KafkaListener���:
    [1]ͬʱ�������Զ��Topic����Ϣ: @KafkaListener(topics = "topic1, topic2")
    [2]@Headerע���ȡ��ǰ��Ϣ�����ĸ�����: 
        @KafkaListener(topics = "test", groupId = "test-consumer")
        public void listen(@Payload String message,
                        @Header(KafkaHeaders.RECEIVED_PARTITION_ID) int partition) {
            logger.info("������Ϣ: {}��partition��{}", message, partition);
        }
    [3]ָ��ֻ���������ض���������Ϣ:
        @KafkaListener(
            groupId = "test-consumer",
            topicPartitions = @TopicPartition(
                topic = "test",
                partitionOffsets = {
                    @PartitionOffset(partition = "0", initialOffset = "0")
                }
            )
        )
        public void listen(@Payload String message,
                        @Header(KafkaHeaders.RECEIVED_PARTITION_ID) int partition) {
            logger.info("������Ϣ: {}��partition��{}", message, partition);
        }
       �������Ҫָ��initialOffset,���������Լ�Ϊ:
        @KafkaListener(groupId = "test-consumer", 
            topicPartitions = @TopicPartition(topic = "test", partitions = { "0", "1" }))
5.Ϊ��Ϣ���������Ϣ������: setRecordFilterStrategy(RecordFilterStrategy<K, V> strategy)
    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> 
        kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory
                = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        // ��ӹ�������
        factory.setRecordFilterStrategy( r -> r.value().contains("fuck"));
        return factory;
    }
    // RecordFilterStrategy�ӿ�: (�Ǻ���ʽ�ӿ�)
    public interface RecordFilterStrategy<K, V> {
        boolean filter(ConsumerRecord<K, V> var1);
    }
6.���͸��ӵ���Ϣ: (ͨ���Զ�����Ϣת���������͸��ӵ���Ϣ)
    [1]������Ϣʵ��: 
        public class Message implements Serializable {
            private static final long serialVersionUID = 6678420965611108427L;
            private String from;
            private String message;
            public Message() { }
            public Message(String from, String message) {
                this.from = from;
                this.message = message;
            }
            @Override
            public String toString() {
                return "Message{" +
                        "from='" + from + '\'' +
                        ", message='" + message + '\'' +
                        '}';
            }
            // get set ��
        }
    [2]������Ϣ����������:
        @Configuration
        public class KafkaProducerConfig {
            @Value("${spring.kafka.bootstrap-servers}")
            private String bootstrapServers;
            // ��������ΪProducerFactory<String,Message>
            @Bean
            public ProducerFactory<String, Message> producerFactory() {
                Map<String, Object> configProps = new HashMap<>();
                configProps.put(
                        ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
                        bootstrapServers);
                configProps.put(
                        ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
                        StringSerializer.class);
                configProps.put(
                        //��value���л�����ָ��Ϊ��Kafka�ṩ��JsonSerializer
                        ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
                        JsonSerializer.class);
                return new DefaultKafkaProducerFactory<>(configProps);
            }
            // ��������ΪKafkaTemplate<String, Message>
            @Bean
            public KafkaTemplate<String, Message> kafkaTemplate() {
                return new KafkaTemplate<>(producerFactory());
            }
        }
    [3]��controller�з��͸�����Ϣ:
        @RestController
        public class SendMessageController {
            private Logger logger = LoggerFactory.getLogger(this.getClass());
            @Autowired
            private KafkaTemplate<String, Message> kafkaTemplate;
            @GetMapping("send/{message}")
            public void sendMessage(@PathVariable String message) {
                this.kafkaTemplate.send("test", new Message("kimi", message));
            }
        }
    [4]�޸�����������:
        @EnableKafka
        @Configuration
        public class KafkaConsumerConfig {
            @Value("${spring.kafka.bootstrap-servers}")
            private String bootstrapServers;
            @Value("${spring.kafka.consumer.group-id}")
            private String consumerGroupId;
            @Value("${spring.kafka.consumer.auto-offset-reset}")
            private String autoOffsetReset;
            @Bean
            public ConsumerFactory<String, Message> consumerFactory() {
                Map<String, Object> props = new HashMap<>();
                props.put(
                        ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,
                        bootstrapServers);
                props.put(
                        ConsumerConfig.GROUP_ID_CONFIG,
                        consumerGroupId);
                props.put(
                        ConsumerConfig.AUTO_OFFSET_RESET_CONFIG,
                        autoOffsetReset);
                return new DefaultKafkaConsumerFactory<>(
                        props,
                        new StringDeserializer(),
                        new JsonDeserializer<>(Message.class));
            }
            @Bean
            public ConcurrentKafkaListenerContainerFactory<String,Message> 
                kafkaListenerContainerFactory(){
                ConcurrentKafkaListenerContainerFactory<String, Message> factory
                        = new ConcurrentKafkaListenerContainerFactory<>();
                factory.setConsumerFactory(consumerFactory());
                return factory;
            }
        }
    [5]�޸���Ϣ����:
        @Component
        public class KafkaMessageListener {
            private Logger logger = LoggerFactory.getLogger(this.getClass());
            // ָ���������������������
            @KafkaListener(topics = "test", groupId = "test-consumer")
            public void listen(Message message) {
                logger.info("������Ϣ: {}", message);
            }
        }
7.��������: 
(https://docs.spring.io/spring-boot/docs/2.1.1.RELEASE/reference/htmlsingle/#common-application-properties)
    # APACHE KAFKA (KafkaProperties)
    spring.kafka.admin.client-id= 
    spring.kafka.admin.fail-fast=false 
    spring.kafka.admin.properties.*= 
    spring.kafka.admin.ssl.key-password= 
    spring.kafka.admin.ssl.key-store-location= 
    spring.kafka.admin.ssl.key-store-password= 
    spring.kafka.admin.ssl.key-store-type= 
    spring.kafka.admin.ssl.protocol= 
    spring.kafka.admin.ssl.trust-store-location= 
    spring.kafka.admin.ssl.trust-store-password= 
    spring.kafka.admin.ssl.trust-store-type= 
    spring.kafka.bootstrap-servers= 
    spring.kafka.client-id= 
    spring.kafka.consumer.auto-commit-interval= 
    spring.kafka.consumer.auto-offset-reset= 
    spring.kafka.consumer.bootstrap-servers= 
    spring.kafka.consumer.client-id= 
    spring.kafka.consumer.enable-auto-commit= 
    spring.kafka.consumer.fetch-max-wait= 
    spring.kafka.consumer.fetch-min-size= 
    spring.kafka.consumer.group-id= 
    spring.kafka.consumer.heartbeat-interval= 
    spring.kafka.consumer.key-deserializer= 
    spring.kafka.consumer.max-poll-records= 
    spring.kafka.consumer.properties.*= 
    spring.kafka.consumer.ssl.key-password= 
    spring.kafka.consumer.ssl.key-store-location= 
    spring.kafka.consumer.ssl.key-store-password= 
    spring.kafka.consumer.ssl.key-store-type= 
    spring.kafka.consumer.ssl.protocol= 
    spring.kafka.consumer.ssl.trust-store-location= 
    spring.kafka.consumer.ssl.trust-store-password= 
    spring.kafka.consumer.ssl.trust-store-type= 
    spring.kafka.consumer.value-deserializer= 
    spring.kafka.jaas.control-flag=required 
    spring.kafka.jaas.enabled=false 
    spring.kafka.jaas.login-module=com.sun.security.auth.module.Krb5LoginModule 
    spring.kafka.jaas.options= 
    spring.kafka.listener.ack-count= 
    spring.kafka.listener.ack-mode= 
    spring.kafka.listener.ack-time= 
    spring.kafka.listener.client-id= 
    spring.kafka.listener.concurrency= 
    spring.kafka.listener.idle-event-interval= 
    spring.kafka.listener.log-container-config= 
    spring.kafka.listener.monitor-interval= 
    spring.kafka.listener.no-poll-threshold= 
    spring.kafka.listener.poll-timeout= 
    spring.kafka.listener.type=single 
    spring.kafka.producer.acks= 
    spring.kafka.producer.batch-size= 
    spring.kafka.producer.bootstrap-servers= 
    spring.kafka.producer.buffer-memory= 
    spring.kafka.producer.client-id= 
    spring.kafka.producer.compression-type= 
    spring.kafka.producer.key-serializer= 
    spring.kafka.producer.properties.*= 
    spring.kafka.producer.retries= 
    spring.kafka.producer.ssl.key-password= 
    spring.kafka.producer.ssl.key-store-location= 
    spring.kafka.producer.ssl.key-store-password= 
    spring.kafka.producer.ssl.key-store-type= 
    spring.kafka.producer.ssl.protocol= 
    spring.kafka.producer.ssl.trust-store-location= 
    spring.kafka.producer.ssl.trust-store-password= 
    spring.kafka.producer.ssl.trust-store-type= 
    spring.kafka.producer.transaction-id-prefix= 
    spring.kafka.producer.value-serializer= 
    spring.kafka.properties.*= 
    spring.kafka.ssl.key-password= 
    spring.kafka.ssl.key-store-location= 
    spring.kafka.ssl.key-store-password= 
    spring.kafka.ssl.key-store-type= 
    spring.kafka.ssl.protocol= 
    spring.kafka.ssl.trust-store-location= 
    spring.kafka.ssl.trust-store-password= 
    spring.kafka.ssl.trust-store-type= 
    spring.kafka.streams.application-id= 
    spring.kafka.streams.auto-startup=true 
    spring.kafka.streams.bootstrap-servers= 
    spring.kafka.streams.cache-max-size-buffering= 
    spring.kafka.streams.client-id= 
    spring.kafka.streams.properties.*= 
    spring.kafka.streams.replication-factor= 
    spring.kafka.streams.ssl.key-password= 
    spring.kafka.streams.ssl.key-store-location= 
    spring.kafka.streams.ssl.key-store-password= 
    spring.kafka.streams.ssl.key-store-type= 
    spring.kafka.streams.ssl.protocol= 
    spring.kafka.streams.ssl.trust-store-location= 
    spring.kafka.streams.ssl.trust-store-password= 
    spring.kafka.streams.ssl.trust-store-type= 
    spring.kafka.streams.state-dir= 
    spring.kafka.template.default-topic= 
```