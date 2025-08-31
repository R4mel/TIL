# [Java] SLF4J, Logback, Log4j

SLF4J(Simple Loggin Facade for Java)는 이름에서 확인할 수 있듯이, java.util.logging, logback 및 log4j와 같은 다양한 로깅 프레임워크에 대한 추상화(인터페이스) 역할을 하는 라이브러리

SLF4J는 추상 로깅 프레임워크이기 때문에 단독으로 사용 X

즉, 최종 사용자가 배포 시 원하는 로깅 프레임워크를 결정하고 사용해도 SLF4J가 인터페이스화 되어있기에, SLF4J를 의존하는 클라이언트 코드에서는 실제 구현을 몰라도 된다.(DIP, 의존관계 역전 법칙)

slf4j-api: 로깅 인터페이스(추상화)

slf4j-impl: 특정 로깅 구현체(Logback, Log4j, JUL → SLF4J 어댑터)

SLF4J 동작과정

- 개발할 때, SLF4J API를 사용하여 로깅 코드를 작성
- 배포할 때, 바인딩된 Loggin Framework가 실제 로깅 코드를 수행

![image.png](%5BJava%5D%20SLF4J,%20Lobgack,%20Log4j%2025bdbeb48cf5809b89dee7558b571a84/image.png)

# SLF4J Bridgin Modules

![image.png](%5BJava%5D%20SLF4J,%20Lobgack,%20Log4j%2025bdbeb48cf5809b89dee7558b571a84/image%201.png)

(SLF4J 이외의) 다른 로깅 API로의 Logger 호출을 SLF4J 인터페이스로 연결(redirect)하여 SLF4J API가 대신 처리할 수 있도록 하는 일종의 어댑터 역할을 하는 라이브러리

아직 변경되지 않은 이전의 레거시 로깅 프레임워크를 위한 라이브러리

Bridge는 여러 개를 사용해도 상관없지만, 사용 시 주의점은 Bridge와 Binder에 같은 종류의 프레임워크를 사용하면 안된다.

# SLF4J API(인터페이스)

![image.png](%5BJava%5D%20SLF4J,%20Lobgack,%20Log4j%2025bdbeb48cf5809b89dee7558b571a84/image%202.png)

로깅에 대한 추상 레이어(인터페이스)를 제공한다. 즉 로깅 동작에 대한 역할을 수행할 추상 메서드를 제공

추상 클래스이기 때문에 이 라이브러리만 단독으로 사용 X

사용 시 주의점은 반드시 하나의 API에 하나의 Binding을 둬야한다.

# SLF4J Binding(.jar)

![image.png](%5BJava%5D%20SLF4J,%20Lobgack,%20Log4j%2025bdbeb48cf5809b89dee7558b571a84/image%203.png)

SLF4J 인터페이스를 로깅 구현체(Logging Framework)와 연결하는 어댑터 역할을 하는 라이브러리

SLF4J API를 구현한 클래스에서 Binding으로 연결될 Logger의 API를 호출

사용 시 주의점은 반드시 하나의 API에 하나의 Binding을 둬야한다.

참고 자료

https://livenow14.tistory.com/63

# Java Standard Logging

- Java에서 제공하는 표준 로깅 라이브러리를 사용할 수 있다.
- JUL(java.util.logging) 줄여서 표현

## Log Level

- SERVER(최고) → WARNING → INFO → FINE → FINER →FINEST (최저)

| SERVER **(highest)** | SEVER is a message level indicating a serious failure.     |
| --- |------------------------------------------------------------|
| WARNING | WARNING is a message level indicating a potential problem. |
| INFO | INFO is a message level for informational messages.        |
| FINE | FINE is a message level providing tracing information.     |
| FINER | FINER indicates a fairly detailed tracing message.         |
| FINEST **(lowest)** | FINEST indicates a highly detailed tracing message.        |

장점: JUL은 JDK와 함께 제공된다. 따라서 로깅ㅇ르 위해서 추가로 종속성 없이 사용할 수 있다.

단점: JUL을 이용한 로깅이 외부에서 공식적으로 확립이 안되어있다. 즉 JDK에 로깅관련된 라이브러리가 포함되어 있지만 Java logging의 표준이 되지 못했다.

JUL을 사용하지 않는 이유

- JUL이 출시될 당시 이미 잘 만들어진 log4j가 존재 했음
- open source로 공개된 라이브러리에 비해서 속도가 느림
- message formating 기능이 다른 라이브러리에 비해 부족함

# Logback

- 다양한 Logging framework 중 하나로, log4j보다 향상된 기능을 갖고 있으며 가장 널리 사용되고 있음
- 로그 레벨
    - TRACE = TRACE + DEBUG + INFO + WARN + ERROR
    - DEBUG = DEBUG + INFO + WARN + ERROR
    - error > warn > info > debug > trace
    - error: 요청 처리 중 문제가 발생함을 뜻함
    - warn: 프로그램 실행은 문제가 없지만, 향후 문제를 일으킬 수도 있음을 뜻함
    - info: 정보성 메시지
    - debug: 개발 시 디버그 용도로 사용됨
    - trace: 좀 더 상세한 이벤트를 나타낼 때 사용됨

## Logback 설정 및 사용

1. [https://mvnrepository.com](https://mvnrepository.com/) 에 접속하여 slf4j, logback classic module 검색
2. 각 버전 선택 후 `.jar` 내려받아 lib에 추가
    1. slf4j-api-2.0.7.jar
    2. logback-classic-1.4.8.jar
3. resources 안에 logback.xml 파일 생성 후 설정
    - logback을 이용하면 appender 별로 로그 레벨을 설정해 주거나, 일정 기간이 지난 로그 파일을 지우거나, 로그의 용량을 제한하는 등의 다양한 기능을 사용할 수 있음

```xml
<configuration>
 <!-- 파일로 출력 -->
 <appender name="FILE" class="ch.qos.logback.core.FileAppender">
 <file>myApp.log</file>
 <encoder>
 <pattern>%date [%thread] %level %logger{10} [%file:%line] - %msg%n</pattern>
 </encoder>
 </appender>
<!-- 표준 출력 -->
<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
</appender>

<!-- debug 레벨 설정 -->
<root level="debug">
    <appender-ref ref="FILE"/>
    <appender-ref ref="STDOUT"/>
</root>

</configuration>
```

- `%date`: 로깅 발생 날짜
- `%thread`: 현재 Thread 명
- `%-5level`: 로그 레벨 (5글자)
- `%msg` : 로깅 메세지
- 옵션 관련 참고 페이지 -> [https://logback.qos.ch/manual/layouts.html](https://logback.qos.ch/manual/layouts.html)
    - Log를 출력하고 싶은 곳에 log 찍어 logback 적용 확인

```java
public class MyLogback {
    private static final Logger logger = LoggerFactory.getLogger(MyLogback.class);

    public static int sum(int a, int b) {
        logger.trace("테스트 용 = {}, {}", a, b);
        logger.info("덧셈 연산 수행 : {} + {}", a, b);
        return a+b;
    }

    public static int divide(int a, int b) {
        if(b == 0) {
            logger.warn("덧셈 연산 실패.. 0으로 나눌 수 없음");
            throw new ArithmeticException("0으로 나눌 수 없습니다.");
        }
        return a/b;
    }

    public static void main(String[] args) {
        int a = 1;
        int b = 2;

        System.out.println(sum(a, b));
        System.out.println(divide(a, 0));
    }

}
```

- **실행결과**
    - MyApp.log 파일 생성되어 설정한 log 기록 남음
    - 설정한 형식으로 아래와 같이 표준 출력

```
15:35:27.766 [main] INFO  com.nhnacademy.edu.MyLogback - 덧셈 연산 수행 : 1 + 2
3
15:35:27.768 [main] WARN  com.nhnacademy.edu.MyLogback - 덧셈 연산 실패.. 0으로 나눌 수 없음
Exception in thread "main" java.lang.ArithmeticException: 0으로 나눌 수 없습니다.
        at com.nhnacademy.edu.MyLogback.divide(MyLogback.java:18)
        at com.nhnacademy.edu.MyLogback.main(MyLogback.java:28)
```