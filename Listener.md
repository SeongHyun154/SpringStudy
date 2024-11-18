# Spring Listener

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
