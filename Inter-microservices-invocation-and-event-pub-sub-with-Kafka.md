Inter microservices invocation
------
microservices 들 간의 내부 호출(invocation) 방법은 여러가지가 있다.  

1. 외부에서 호출을 하듯이 REST API 를 호출하는 방법  
2. FeignClient 를 통하여 바로 서비스를 접근방법(spring-cloud)  
3. Publish–subscribe pattern 을 사용한 Event Driven 방법  


1번 방법은 Spring에서 예를 들자면 `@RestController` 와 `@RequestMapping` 을 사용한 호출 방법이다.  
외부 서비스들을 연결할때 많이 쓰이는 방법이기에, 내부시스템도 물론 사용이 가능하지만,  
각 microservices 의 ip 및 domain 을 기억해야 한다.  

2번 방법은 `@FeignClient("서비스명")` 을 사용한 방법인데,  
여기서 서비스명은 eureka 에 등록이 되어있어야한다.  
이 방법은 기존에 설명 하였던 [[Integration with Feign Client]] 와 [spring-cloud-netflix-feign-tutorial](http://www.javainuse.com/spring/spring-cloud-netflix-feign-tutorial) 에서 자세한 설명을 참고할 수 있다.  
이 방법은 해당 마이크로 서비스를 가지고 와서 local 객체처럼 사용 할 수 있는 장점이 있다.  

이번시간에 집중할 내용은 3번 방법이다.  
1번과 2번 방법은 호출 기반 아키텍처(Request-Driven Architecture)다. 이와 같이 설계시 서비스를  
교체시 영향을 미치는 요소가 많을 수 밖에 없다.  
3번 방법은 이벤트 중심 아키텍처(EDA, Event-Driven Architecture)인데, 이는 이벤트가 발행(Publish)되고,  
이 이벤트가 필요한 서비스에서 이벤트를 수신(subscribe)하여 사용하는 방법이다.  

쉬운 예를 들자면,  
쇼핑몰에서 고객이 물건을 `주문`하고 나면, `포인트`,`업적`,`축하메일`,`배송`,`장바구니` 등의 서비스가  
연달아 실행을 해야 한다고 가정을 하자. 이를 호출기반 아케틱쳐로 구현을 한다면, 5개의 서비스의  
서비스명이나, doamin 명 등을 모두 알고 있어야 하고, 모두 call을 해야한다.  
이것을 event pub sub 으로 변경한다면, `주문`서비스에서는 이벤트를 발행만 하고,  
나머지 서비스에서는 수신만 하여, 이벤트가 발생 후 해야할 일만 각자 서비스에서 준비하면 된다.  
여기에 microservice 들이 추가된다고 하여도, 기존 시스템에 미치는 영향이 최소화 된다.  

Event Publish–subscribe with Kafka
-----
이벤트를 주고 받을때 사용되는 Event-Broker 에는 Apache ActiveMQ, Apache Kafka, RabbitMQ 등  
여러가지가 있다. 이중에서 spring 에서 많이 쓰이는 Kafka 로 구현방법을 설명하겠다.  

Kafka 에 대한 설명들은 많은 블로그가 있어서 해당 블로그 링크로 대체하겠다.  
핵심개념인 topic 과 partition, Producer(publish)와 Consumer(subscribe) 에 대한 이해가 필요하다.  
> [Kafka 이해하기](https://medium.com/@umanking/%EC%B9%B4%ED%94%84%EC%B9%B4%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0-%ED%95%98%EA%B8%B0%EC%A0%84%EC%97%90-%EB%A8%BC%EC%A0%80-data%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0%ED%95%B4%EB%B3%B4%EC%9E%90-d2e3ca2f3c2)  
> [Apache Kafka 소개 및 아키텍쳐](http://junil-hwang.com/blog/apache-kafka/)  

1. 우선 kafka를 호출하기 위하여 local환경에 kafka를 설치한다.  
카프카는 분산환경인 Apache ZooKeeper 위에서 돌아가기때문에 카프카를 실행전에 ZooKeeper를 먼저 실행해야한다.  
카프카 설치 및 실행 방법은 [https://kafka.apache.org/quickstart](https://kafka.apache.org/quickstart) 를 따라하면된다.  

2. 이제 우리의 서비스에 kafka 를 통하여 메세지를 발행하고, 수신하는 로직을 작성하여 보자.  


### 이벤트 Producer(publish), Consumer(subscribe) 서비스 공통 작성
#### pom.xml
```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
    <version>1.3.2.RELEASE</version>
</dependency>
```
여기서 주의할점은 현재 (2018년8월) spring-kafka 는 2.x 대 release version 이 나와있다.  
2.x 버전은 spring-boot 2.x 버전에서만 돌아가니, 만약 자기 project가 spring-boot 1.x 를  
사용중이라면 `<version>1.3.x.RELEASE</version>` 로 설정을 해줘야 한다.  

#### application.yml
```yml
spring:
  profiles: local
  kafka:
    bootstrap-servers: localhost:9092
```

### 이벤트 Producer(publish) 서비스 작성
#### PublishConfig.java
```java
@EnableKafka
public class PublishConfig {
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;
    @Bean
    public Map<String, Object> producerConfigs() {
        Map<String, Object> props = new HashMap<String, Object>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return props;
    }
    @Bean
    public ProducerFactory<String, String> producerFactory() {
        return new DefaultKafkaProducerFactory(producerConfigs());
    }
    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<String, String>(producerFactory());
    }
}
```
간단히 설명을 하자면 `@EnableKafka` 를 선언하여 Kafka사용을 spring에 알리고,  
KafkaTemplate 을 `@Bean` 으로 선언 하였다.  

#### Clazz.java
```java
@Autowired
KafkaTemplate<String, String> kafkaTemplate;
public void sendkafka(){
   String topic = "topicName";
   String payload = "메세지 내용";
   kafkaTemplate.send(topic, payload);
}
```

### 이벤트  Consumer(subscribe) 서비스 작성
#### SubscribeConfig.java
```java
@EnableKafka
public class SubscribeConfig {
    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;
    @Bean
    public Map<String, Object> consumerConfigs() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "mail");

        return props;
    }
    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        return new DefaultKafkaConsumerFactory<>(consumerConfigs());
    }
    @Bean
    public KafkaListenerContainerFactory<ConcurrentMessageListenerContainer<String, String>> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
```
PublishConfig 와 설정이 비슷해 보이지만 ConsumerConfig 값을 지정하여 주고, GROUP_ID_CONFIG 를  
지정하여 주었다. Consumer 에서 GROUP_ID 는 반드시 지정을 해줘야 한다.  
이는 컨슈머 그룹은 하나의 topic에 대한 책임을 가지고 있기때문이다.  

마지막으로 토픽을 받는 부분이다.
```java
    @KafkaListener(topics = "topicName")
    public void receiveTopic(ConsumerRecord<?, ?> consumerRecord) {
        System.out.println("Receiver on topic: "+consumerRecord.toString());
        String data = (String)consumerRecord.value();
    }
```

