# Spring Scheduled

Spring의 **@Scheduled**는 애플리케이션에서 작업을 일정한 간격으로 실행하거나 특정 시간에 실행하도록 예약할 수 있는 기능을 제공합니다. 이 기능은 **Spring Task Scheduling** 모듈을 기반으로 구현됩니다.

---

## 주요 개념

1. **@Scheduled 어노테이션**
   - 메서드 위에 붙여 작업을 예약 실행할 수 있습니다.
   - 고정된 간격, 특정 시간대, 또는 Cron 표현식을 기반으로 작업을 실행 가능.

2. **Task Scheduler**
   - Spring에서 스케줄링 작업을 관리하는 핵심 컴포넌트.
   - `ThreadPoolTaskScheduler` 등을 사용해 비동기 작업 관리 가능.

3. **@EnableScheduling**
   - `@Scheduled` 어노테이션이 동작하도록 설정하는 어노테이션.
   - 주로 설정 클래스에 선언.

---

## @Scheduled 사용법

### 1. 고정 간격으로 실행 (`fixedRate`)
- 이전 작업 시작 시간으로부터 지정된 간격으로 작업 실행.

### 2. Cron 표현식을 사용한 스케줄링 (cron)
 - **Unix Cron 표현식을 사용하여 복잡한 스케줄 설정 가능.**
 - 초(0-59)  분(0-59)  시(0-23)  일(1-31)  월(1-12)  요일(0-6)
 - 매 초마다 실행: "* * * * * *"
 - 매 시간의 30분마다 실행: "0 30 * * * *"
 - 매일 오전 8시 30분마다 실행: "0 30 8 * * *"
 - 매주 월요일 아침 9시 30분마다 실행: "0 30 9 * * 1"
 - 매월 1일 아침 4시 30분마다 실행: "0 30 4 1 * *"
 - 매주 월요일부터 금요일까지 아침 10시마다 실행: "0 0 10 * * 1-5"
  
   
| 표현식            | 설명                     |
|-------------------|--------------------------|
| `0 0 12 * * *`    | 매일 12시 정각 실행      |
| `0 15 10 * * ?`   | 매일 10:15에 실행        |
| `0 0/5 * * * ?`   | 5분마다 실행             |
| `0 0 0 1 1 ?`     | 매년 1월 1일 0시 정각 실행 |



---
#### 예시 코드
```java
package com.example.ex01.scheduled;

import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
@EnableScheduling
public class Scheduling {
	
//	@Scheduled(fixedRate = 1000)
//	public void t1() {
//		System.out.println("Scheduling's t1() invoke....");
//	}

	@Scheduled(cron="*/15 * * * * *")
	public void t2() {
		System.out.println("Scheduling's t2() invoke....");
	}
	
}
```