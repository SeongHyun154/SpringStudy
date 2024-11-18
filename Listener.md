# :pencil2: Spring Listener

Spring Listener는 **Spring Framework**에서 특정 이벤트(Event)가 발생했을 때 이를 감지하고 처리하기 위해 사용하는 컴포넌트입니다. 주로 이벤트 기반 아키텍처에서 활용되며, 애플리케이션 내 이벤트 처리와 외부 시스템 상호작용에 유용합니다.

---

## 주요 개념

1. **이벤트 (Event)**  
   - Spring에서 이벤트는 `ApplicationEvent` 클래스를 상속받아 정의됩니다.  
     예: 데이터 저장, 상태 변경 등의 상황을 나타냄.

2. **리스너 (Listener)**  
   - 이벤트가 발생했을 때 이를 감지하고 처리하는 컴포넌트.  
   - `ApplicationListener` 인터페이스를 구현하거나, `@EventListener` 어노테이션을 사용하여 정의.

---

## 구현 방법

### 1. `ApplicationListener` 인터페이스 구현
```java
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class MyEventListener implements ApplicationListener<MyCustomEvent> {

    @Override
    public void onApplicationEvent(MyCustomEvent event) {
        System.out.println("이벤트 수신: " + event.getMessage());
    }
}
```

### 2. @EventListener 어노테이션 사용
```java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class MyEventListener {

    @EventListener
    public void handleCustomEvent(MyCustomEvent event) {
        System.out.println("이벤트 처리: " + event.getMessage());
    }
}

```

---

## 이벤트 생성 및 발행

### 1. 이벤트 클래스 정의

```java
import org.springframework.context.ApplicationEvent;

public class MyCustomEvent extends ApplicationEvent {
    private String message;

    public MyCustomEvent(Object source, String message) {
        super(source);
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}
```

### 2. 이벤트 발행
#### ● ApplicationEventPublisher를 사용하여 이벤트를 발행.
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Component;

@Component
public class EventPublisher {

    @Autowired
    private ApplicationEventPublisher applicationEventPublisher;

    public void publishEvent(String message) {
        MyCustomEvent event = new MyCustomEvent(this, message);
        applicationEventPublisher.publishEvent(event);
    }
}
```
---
## Spring Listener의 특징 및 장점

### 1. 비동기 처리

#### ● 이벤트 리스너를 비동기로 처리하려면 @Async 어노테이션을 사용.
```java
@EventListener
@Async
public void handleAsyncEvent(MyCustomEvent event) {
    System.out.println("비동기 이벤트 처리: " + event.getMessage());
}
```

### 2. 강력한 확장성
#### ● 다양한 이벤트를 관리하고 모니터링할 수 있어 유지보수가 용이.

### 3. 다양한 이벤트 타입 지원

#### ● 사용자 정의 이벤트 및 Spring 기본 이벤트(ContextRefreshedEvent, ContextClosedEvent 등) 처리 가능.


### 3.활용 예

- **사용자 활동 로깅**  
  사용자의 특정 행동(로그인, 파일 업로드 등)을 이벤트로 기록하여 로그를 생성.

- **알림 시스템**  
  특정 이벤트(주문 완료, 메시지 수신 등)에 대해 알림(이메일, 푸시 알림 등)을 전송.

- **비동기 이벤트 처리**  
  시간 소모적인 작업(이미지 처리, 데이터 백업 등)을 비동기 이벤트로 처리하여 성능 향상.

- **마이크로서비스 간 메시징**  
  이벤트를 통해 마이크로서비스 간 데이터를 전송하거나 상태를 동기화.

Spring Listener는 이벤트 기반 아키텍처를 활용한 **유연하고 모듈화된 시스템 설계**를 가능하게 합니다.

---
---
---
## 예시 코드

- **Spring MVC 환경에서 다양한 설정을 구성하는 예제입니다. 주요 설정은 WebMvcConfig 클래스에서 이루어지며, Listener, Interceptor, HandlerMapping 등의 개념이 활용**


# WebMvcConfig 클래스

이 클래스는 Spring MVC 설정을 담당하며 `@Configuration`과 `@EnableWebMvc`로 설정 클래스임을 명시합니다.

---

## 주요 설정 내용

### 1.1 멀티파트 설정 (`multipartResolver`)
파일 업로드 처리를 담당하는 설정입니다.

- `setMaxUploadSize`: 전체 업로드 가능 용량을 설정. (20MB)
- `setMaxUploadSizePerFile`: 파일 하나의 최대 업로드 용량. (20MB)
- `setMaxInMemorySize`: 메모리에 보관할 데이터의 크기.

---

### 1.2 뷰 리졸버 설정 (`viewResolver`)
JSP 뷰 파일을 찾기 위한 설정입니다.

- `setPrefix`: JSP 파일이 위치하는 디렉터리 지정. (`/WEB-INF/views/`)
- `setSuffix`: JSP 파일의 확장자 지정. (`.jsp`)

---

### 1.3 정적 리소스 핸들러 (`addResourceHandlers`)
정적 자원(css, js, 이미지 등)의 경로를 설정합니다.

- `"/resources/**"`로 요청이 들어오면 `/resources/` 경로에서 파일을 찾음.

---

### 1.4 인터셉터 설정 (`addInterceptors`)
특정 URL 요청을 가로채서 사전 처리/후처리 로직을 추가합니다.

- `MemoInterceptor`는 `/memo/*` 패턴에 대해 동작.


### 2. Listener 등록
- **Listener는 특정 이벤트가 발생했을 때 자동으로 실행되는 구성 요소입니다.**
- 아래 세 가지 Listener가 등록
#### 2.1 RequestHandledEventListener
- Spring이 제공하는 RequestHandledEvent를 처리합니다.
- HTTP 요청이 완료되었을 때 호출됩니다.

```java
@Override
public void onApplicationEvent(RequestHandledEvent event) {
    System.out.println("Request 처리 완료 이벤트: " + event.getSource());
}
```
