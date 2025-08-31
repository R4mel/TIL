# Network

## **학습목표**

- **소켓 통신 이해**
    - 소켓을 사용하여 클라이언트와 서버 간의 데이터 통신을 이해하고 구현합니다.
- **멀티스레딩**
    - 여러 클라이언트의 요청을 동시에 처리하기 위해 멀티스레딩을 사용합니다. 이를 통해 동시성 프로그래밍의 개념을 익힙니다.
- **서버 아키텍처 이해**
    - 웹서버의 기본 아키텍처와 작동 방식을 이해합니다. 클라이언트의 요청을 받아들이고, 이를 처리하여 응답을 반환하는 과정을 학습합니다.
- **Observer(관찰자/감시자) Pattern 이해**
    - Observer pattern 의 개념과 이를 실제 코드에 적용하는 방법을 학습합니다.
- **이벤트 기반 프로그래밍**
    - 메시지 수신 시 이벤트를 발생시키고 이를 처리하는 방법을 익힙니다.
    - 간단하나 GUI 기반으로 이벤트 기반 프로그래밍 하는 방법을 익힙니다.

# Socket

**Socket 통신**

소켓 통신은 네트워크 상에서 두 프로그램 간의 통신을 가능하게 하는 기술입니다. 소켓은 IP 주소와 Port 번호로 구성되며, 클라이언트와 서버 간의 데이터 전송을 담당합니다.

**IP 주소 (Internet Protocol Address)**

IP 주소는 네트워크 상에서 장치를 식별하는 고유한 주소입니다. IP 주소는 두 가지 버전이 있습니다:

1. **IPv4**: 32비트 주소 체계로, 4개의 8비트 숫자로 구성됩니다.
    - 예: `192.168.0.1`
2. **IPv6**: 128비트 주소 체계로, 8개의 16비트 숫자로 구성됩니다.
    - 예: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`

IP 주소는 네트워크 상에서 데이터를 전송할 때 송신자와 수신자를 식별하는 데 사용됩니다.

**포트 (Port)**

포트는 IP 주소와 결합되어 특정 네트워크 서비스나 애플리케이션을 식별하는 숫자입니다. 포트 번호는 0부터 65535까지의 범위를 가지며, 주로 다음과 같이 분류됩니다:

1. **잘 알려진 포트 (Well-known Ports)**
    - 0-1023 범위의 포트 번호로, HTTP(80), HTTPS(443), FTP(21) 등과 같은 표준 서비스에 할당됩니다.
2. **등록된 포트 (Registered Ports)**
    - 1024-49151 범위의 포트 번호로, 특정 애플리케이션이나 서비스에 할당됩니다.
3. **동적/사설 포트 (Dynamic/Private Ports)**
    - 49152-65535 범위의 포트 번호로, 임시 연결에 사용됩니다.

**IP 주소와 Port의 결합**

- IP 주소와 Port 번호는 함께 사용되어 네트워크 상에서 특정 애플리케이션을 식별합니다. 예를 들어, `192.168.0.1:8080`은 IP 주소 `192.168.0.1`의 포트 번호 `8080`에서 실행되는 애플리케이션을 나타냅니다.
- 이렇게 IP 주소와 포트 번호를 사용하여 네트워크 상에서 데이터를 주고받는 애플리케이션을 식별하고 통신을 수행할 수 있습니다.

**Socket의 종류**

- **TCP 소켓**: 신뢰성 있는 연결 지향형 통신을 제공합니다.
- **UDP 소켓**: 비연결형 통신을 제공하며, 데이터그램을 사용합니다.

# **TCP(Transmission Control Protocol)**

TCP(Transmission Control Protocol)는 인터넷 프로토콜 스위트의 핵심 프로토콜 중 하나로, 신뢰성 있는 데이터 전송을 보장하는 연결 지향 프로토콜입니다. TCP는 데이터의 전송 순서와 무결성을 보장하며, 패킷 손실 시 재전송을 통해 데이터의 완전성을 유지합니다.

**주요 특징**

- **연결 지향(Connection-oriented)**
    - TCP는 데이터 전송 전에 송신자와 수신자 간의 연결을 설정합니다.
- **신뢰성(Reliability)**
    - 데이터가 손실되거나 손상된 경우 재전송을 통해 데이터의 무결성을 보장합니다.
- **흐름 제어(Flow Control)**
    - 송신자가 수신자의 처리 능력을 초과하지 않도록 데이터 전송 속도를 조절합니다.
- **혼잡 제어(Congestion Control)**
    - 네트워크 혼잡을 방지하기 위해 데이터 전송 속도를 조절합니다.

**TCP 헤더 구조**

TCP 헤더는 데이터 전송을 관리하기 위한 다양한 필드를 포함하고 있습니다. 주요 필드는 다음과 같습니다:

- **소스 포트(Source Port)**
    - 송신자의 포트 번호
- **목적지 포트(Destination Port)**
    - 수신자의 포트 번호
- **순서 번호(Sequence Number)**
    - 데이터의 순서를 나타내는 번호
- **확인 응답 번호(Acknowledgment Number)**
    - 수신자가 다음에 받을 데이터의 순서 번호
- **데이터 오프셋(Data Offset)**
    - TCP 헤더의 길이
- **제어 비트(Control Bits)**
    - 연결 설정, 종료, 데이터 전송 등의 제어 정보를 포함
- **윈도우 크기(Window Size)**
    - 수신자가 한 번에 받을 수 있는 데이터의 양
- **체크섬(Checksum)**
    - 데이터의 무결성을 확인하기 위한 값
- **긴급 포인터(Urgent Pointer)**
    - 긴급 데이터의 위치를 나타내는 포인터

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image.png)

**TCP 연결 / 종료**

TCP 연결은 3 way handshake(세 단계)의 과정을 통해 설정됩니다:

1. **SYN**
    - 송신자가 연결 요청을 보냅니다.
2. **SYN-ACK**
    - 수신자가 연결 요청을 수락하고 응답을 보냅니다.
3. **ACK**
    - 송신자가 수신자의 응답을 확인하고 연결이 설정됩니다.

**TCP 연결 / 종료**

TCP 연결은 3 way handshake(세 단계)의 과정을 통해 설정됩니다:

1. **SYN**
    - 송신자가 연결 요청을 보냅니다.
2. **SYN-ACK**
    - 수신자가 연결 요청을 수락하고 응답을 보냅니다.
3. **ACK**
    - 송신자가 수신자의 응답을 확인하고 연결이 설정됩니다.

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%201.png)

연결 종료는 4 way handshake(네 단계)의 과정을 통해 이루어집니다:

1. **FIN**
    - 송신자가 연결 종료 요청을 보냅니다.
2. **ACK**
    - 수신자가 연결 종료 요청을 확인합니다.
3. **FIN**
    - 수신자가 연결 종료 요청을 보냅니다.
4. **ACK**
    - 송신자가 수신자의 연결 종료 요청을 확인하고 연결이 종료됩니다.

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%202.png)

# TCP 기반의 Server/Client 구현

**echo server란?**

에코 서버는 클라이언트가 전송한 데이터를 그대로 되돌려 보내는 서버를 의미합니다. 클라이언트가 서버로 데이터를 전송하면, 서버는 그 데이터를 받아서 그대로 다시 클라이언트에게 전송합니다.
이러한 서버는 주로 네트워크 연결을 테스트하거나 디버깅하는 데 사용됩니다. 예를 들어, 클라이언트가 서버에 "hello"라는 메시지를 보내면, 서버는 "hello"라는 동일한 메시지를 클라이언트에게 다시 보냅니다.

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%203.png)

# Server Socket

**서버 소켓 생성**

ServerSocket 클래스를 사용하여 서버 소켓을 생성할 수 있습니다:

```java
// 특정 포트에 바인딩된 서버 소켓 생성
ServerSocket serverSocket = new ServerSocket(8080);

// 백로그 큐 크기를 지정하여 생성
ServerSocket serverSocket = new ServerSocket(8080, 50);

// 특정 IP 주소에 바인딩하여 생성
ServerSocket serverSocket = new ServerSocket(8080, 50, InetAddress.getByName("192.168.1.1"));
```

**Port '0' 사용**

포트 번호를 0으로 지정하면, 시스템이 사용 가능한 포트를 자동으로 할당합니다:

```java
ServerSocket serverSocket = new ServerSocket(0);
System.out.println("할당된 포트: " + serverSocket.getLocalPort());
```

이는 테스트 환경이나 포트 충돌을 피하고 싶을 때 유용합니다. 시스템은 사용 가능한 임시 포트(ephemeral port)를 동적으로 할당합니다.

**TCP Backlog Queue**

백로그 큐는 서버가 TCP 연결 요청을 처리하기 위해 유지하는 대기열입니다. 이는 서버가 accept()를 호출하기 전까지 대기하는 연결 요청들을 저장합니다.

Java에서 ServerSocket 생성 시 backlog 큐 크기를 설정할 수 있지만, 실제 적용되는 크기는 운영체제의 설정에 따라 달라질 수 있습니다. 운영체제가 Java에서 요청한 backlog 값을 수용하거나 무시할 수 있으므로, 정확한 backlog 크기 설정을 위해서는 운영체제 수준에서 직접 설정하는 것이 필요합니다.

**TCP Backlog Queue vs BlockingQueue**

TCP Backlog Queue와 Java의 BlockingQueue는 다음과 같은 차이점이 있습니다:

- **구현 레벨**
    - TCP Backlog Queue: 운영체제 커널 레벨에서 TCP 연결 요청을 관리
    - BlockingQueue: Java 애플리케이션 레벨에서 데이터를 관리
- **용도**
    - TCP Backlog Queue: TCP 연결 요청만을 위한 대기열
    - BlockingQueue: 모든 종류의 객체를 저장할 수 있는 범용 대기열
- **제어**
    - TCP Backlog Queue: 운영체제가 주도적으로 관리
    - BlockingQueue: 애플리케이션에서 완전한 제어 가능
- **동작 방식**
    - TCP Backlog Queue: SYN 패킷으로 시작되는 TCP 연결 요청을 큐에 저장
    - BlockingQueue: put()/take() 메서드로 스레드 간 안전한 데이터 교환 지원

**운영체제별 백로그 확인 방법**

***Linux***

```bash
# 현재 백로그 큐 크기 확인
sysctl net.core.somaxconn

# TCP 연결 대기열 상태 확인
netstat -an | grep SYN_RECV
```

***MacOS***

```bash
# TCP 백로그 설정 확인
sysctl kern.ipc.somaxconn
```

***Windows***

```bash
# TCP 연결 상태 확인
netstat -an | find "LISTENING"
```

**백로그 설정 방법**

***Linux***

```bash
# 임시 설정
sudo sysctl -w net.core.somaxconn=1024

# 영구 설정 (/etc/sysctl.conf 수정)
net.core.somaxconn=1024
```

***MacOS***

```bash
# 임시 설정
sudo sysctl -w kern.ipc.somaxconn=1024
```

***Windows***

```bash
# 레지스트리 수정
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters
TcpNumConnections (DWORD)
```

# Method 기반 응답(echo)

**Message 규칙**

`{method} {message}`

- ex)
    - echo hello
    - echo nhnacademy

**Junit5 - @ParameterizedTest**

ParameterizedTest는 동일한 테스트를 여러 다른 입력값으로 실행할 수 있게 해주는 기능입니다.

**@ValueSource 예제**

- 단순한 값들을 테스트할 때 사용합니다.

```java
@ParameterizedTest
@ValueSource(strings = { "hello", "world", "test" })
void echoTest(String input) {
    assertEquals("echo " + input, messageProcessor.process("echo " + input));
}
```

**@CsvSource 예제**

- 여러 개의 인자를 전달하고 싶을 때 사용합니다.

```java
@ParameterizedTest
@CsvSource({
    "echo,hello",
    "echo,world",
    "echo,test"
})
void echoTestWithMethod(String method, String message) {
    assertEquals(method + " " + message,
                messageProcessor.process(method + " " + message));
}
```

**@MethodSource 예제**

- 복잡한 데이터나 객체를 전달하고 싶을 때 사용합니다.

```java
@ParameterizedTest
@MethodSource("provideStringsForEcho")
void echoTestWithMethodSource(String input) {
    assertEquals("echo " + input, messageProcessor.process("echo " + input));
}

static Stream<String> provideStringsForEcho() {
    return Stream.of("hello", "world", "test");
}
```

**@EnumSource 예제**

- Enum 값들을 테스트 케이스로 사용하고 싶을 때 사용합니다.

```java
enum EchoMode {
    NORMAL, UPPERCASE, LOWERCASE
}

@ParameterizedTest
@EnumSource(EchoMode.class)
void echoTestWithEnum(EchoMode mode) {
    String input = "hello";
    String expected = mode == EchoMode.UPPERCASE ? "ECHO HELLO" :
                     mode == EchoMode.LOWERCASE ? "echo hello" :
                     "echo hello";
    assertEquals(expected, messageProcessor.process("echo " + input));
}
```

- [junit5, ParameterizedTest](https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/params/ParameterizedTest.html)
- [baeldung, parameterized-tests-junit-5](https://www.baeldung.com/parameterized-tests-junit-5)

**Test**

- mvn package 명령을 실행 후 client, server를 실행 합니다.
    - pom.xml을 참고하세요.

`mvn package

cd target

java -jar client-shade.jar

java -jar server-shade.jar`

**Server**

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%204.png)

**Client**

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%205.png)

# Method 기반 응답(time)

**Message 규칙**

`{method} {dateformat}`

- ex)
    - time
    - time yyyy-mm-dd

**DateTimeFormatter**

`DateTimeFormatter 클래스 DateTimeFormatter 클래스는 Java 8부터 도입된 새로운 날짜와 시간 API 입니다.`

```
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class DateTimeFormatterExample {
    public static void main(String[] args) {
        LocalDateTime dateTime = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String formattedDateTime = dateTime.format(formatter);
        System.out.println(formattedDateTime);
    }
}
```

**Date Format**

- yyyy: 연도 (4자리)
- MM: 월 (2자리)
- dd: 일 (2자리)
- HH: 시간 (24시간 형식, 2자리)
- mm: 분 (2자리)
- ss: 초 (2자리)
- more …
    - [oracle, DateTimeFormatter](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html)

**Test**

- mvn package 명령을 실행 후 client, server를 실행 합니다.
    - pom.xml을 참고하세요.

`mvn package

cd target

java -jar client-shade.jar

java -jar server-shade.jar`

**Server**

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%206.png)

**Client**

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%207.png)

# Client GUI 적용

**Client**

- Message 수신/송신을 각각의 Thread로 분리

![image.png](Network%20258dbeb48cf5804ba7eef24e124a6b95/image%208.png)

**Observer(관찰자) Pattern**

한 객체의 상태가 변경되면 그 객체에 의존하는 다른 객체들에게 자동으로 알림이 가고, 업데이트가 이루어지도록 하는 패턴

**구성 요소**

1. **Subject (주제)**
    - 상태를 가지고 있으며, 상태가 변경되면 Observer들에게 알림을 보냅니다.
    - Observer들을 등록하고 제거하는 메서드를 제공합니다.
    - Subject interface
        
        ```java
        public interface Subject {
            void register(EventType eventType, Observer observer);
            void remove(EventType eventType, Observer observer);
            void notifyObservers(EventType eventType, String message);
        
            default void sendMessage(String message){
                notifyObservers(EventType.SEND,message);
            }
        
            default void receiveMessage(String message){
                notifyObservers(EventType.RECV,message);
            }
        }
        ```
        
2. **Observer (관찰자)**
    - Subject의 상태 변경을 감지하고, 그에 따라 동작을 수행합니다.
    - Subject로부터 알림을 받기 위한 인터페이스를 구현합니다.
    - Observer interface
        
        ```java
        public interface Observer {
            EventType getEventType();
            void updateMessage(String message);
            default boolean validate(EventType eventType){
                return getEventType().equals(eventType);
            }
        }
        ```