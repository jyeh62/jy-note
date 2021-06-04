---
type: Spring
keywords: ehcache
created : 2021/05/28/18:05
tags : #ehcache, #spring
---

# Apply ehCache

## 적용방법

### 의존성 추가

```groovy
    implementation 'org.springframework.boot:spring-boot-starter-cache' //스프링에서 사용
    implementation 'org.ehcache:ehcache:3.9.4'
    implementation 'javax.cache:cache-api:1.1.1'
```

### 설정 파일 작성

application.properties와 같은 디렉토리에 ehcache.xml 생성 - 파일 변경 가능

xml의 element의 순서가 중요하므로 변경하면 에러가 발생함

```xml
<?xml version="1.0" encoding="UTF-8"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://www.ehcache.org/v3"
        xmlns:jsr107="http://www.ehcache.org/v3/jsr107"
        xsi:schemaLocation="
            http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core-3.0.xsd
            http://www.ehcache.org/v3/jsr107 http://www.ehcache.org/schema/ehcache-107-ext-3.0.xsd">
    <!-- config : XML 구성의 루트 요소이다. -->


    <cache alias="squareCache"> <!-- cache 요소는 CachceManager에 의해 작성되고 관리될 Cache 인스턴스를 나타낸다. Cache<k,v> 형태로 인스턴스가 생성된다. alias에는 캐시의 이름을 지정한다. -->
        <!--<key-type>java.lang.String</key-type>  key-type 요소는 Cache 인스턴스에 저장될 캐시의 키의 FQCN을 지정한다. 즉, 키의 타입을 명시해주면 된다. 기본 값은 java.lang.Object 이다. -->
        <!--<value-type>java.lang.String</value-type>  value-type 요소는 Cache 인스턴스에 저장된 값의 FQCN을 지정한다. 기본 값은 java.lang.Object 이다. -->
        <expiry> <!-- expiry는 캐시 만료기간에 대해 설정하는 요소이다. -->
            <ttl unit="minutes">5</ttl> <!-- ttl에는 캐시 만료 시간을 지정하며 unit에는 단위를 지정한다. 해당 요소는 30초 뒤 캐시가 만료되는 것으로 지정되어 있다. -->
            <!-- unit은 days, hours, minutes, seconds, millis, micros, nanos 를 세팅할 수 있다. -->
        </expiry>
        <listeners> <!-- listeners는 Cache의 리스너를 등록하는 요소이다. -->
            <listener>
                <class>com.b.a.CacheEventLogger</class> <!-- 캐싱처리에 대한 리스너가 등록된 클래스의 FQCN을 등록한다. -->
                <event-firing-mode>ASYNCHRONOUS</event-firing-mode>
                <event-ordering-mode>UNORDERED</event-ordering-mode>
                <events-to-fire-on>CREATED</events-to-fire-on>
                <events-to-fire-on>EXPIRED</events-to-fire-on>
            </listener>
        </listeners>

        <resources>
            <heap unit="entries">5</heap> <!--저장 entry 갯수> -->
        </resources>
    </cache>

</config>
```

### 설정 파일 적용

application.properties에 아래 내용 추가
    spring.cache.jcache.config=classpath:ehcache.xml

## Cache 사용

cache manager를 호출하여 코딩을 할 수 있으며, spring annotationㅇ을 사용하여 메소드 단위로 사용 할 수 있다
MainApplication에 @EnableCaching을 선언 후 사용한다.

```java
    @EnableCaching
    @SpringBootApplication
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
```

### Cache 사용선언

@Cacheable

```java
    @Service
    public class NumberService {

        @Cacheable(cacheNames = "squareCache", key = "#number", condition = "#number > 10")
        public BigDecimal square(Long number) {
            BigDecimal square = BigDecimal.valueOf(number).multiply(BigDecimal.valueOf(number));
            log.info("square of {} is {}", number, square);
            return square;
        }

    }
```

- cacheNames : 설정 파일에 등록한 이름
- key : cache의 key로 사용, 선언하지 않으면 알아서 지정됨
  - keyGenerator를 사용하여 key 생성하는 방법을 정의할 수 있다.
- condition : cache를 사용할 조건을 명시

### Cache 로그

xml 파일에 등록한 listener를 구현하여 cache event를 처리할 수 있다.

```kotlin
    class CacheEventLogger : CacheEventListener<Any?, Any?> {
        private val logger = LogFactory.getLog(CacheEventLogger::class.java)

        override fun onEvent(cacheEvent: CacheEvent<out Any?, out Any?>) {
            logger.info("cache event logger message. getKey: {${cacheEvent.key}} / getOldValue: {${cacheEvent.oldValue} / getNewValue:{${cacheEvent.newValue}}")
        }
    }
```

## 주의사항

같은 서비스(클래스) 내에서는 cache 선언한 메소드를 호출해도 cache를 사용하지 않는다. 따라서 별도의 클래스로 만들어야한다

## 참고

<https://medium.com/finda-tech/spring-%EB%A1%9C%EC%BB%AC-%EC%BA%90%EC%8B%9C-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-ehcache-4b5cba8697e0>