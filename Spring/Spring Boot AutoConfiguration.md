### ✅ Spring Boot AutoConfiguration 동작 원리

Spring Boot의 AutoConfiguration은 필요한 설정을 개발자가 명시하지 않아도,  
클래스패스에 존재하는 라이브러리와 환경 정보에 따라 자동으로 구성해주는 기능이다.  
즉, Spring Boot는 상황에 맞는 설정을 조건적으로 적용함으로써 설정의 복잡도를 줄이고 개발을 빠르게 시작할 수 있도록 돕는다.

---

#### 1. 진입점: `@SpringBootApplication`

```java
@SpringBootApplication
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(Application.class, args);
  }
}
```

- `@SpringBootApplication`에는 `@EnableAutoConfiguration`이 포함되어 있으며,  
  이 애노테이션이 자동 설정의 진입점 역할을 한다.

---

#### 2. 내부 메커니즘: `AutoConfigurationImportSelector`

- `@EnableAutoConfiguration`은 내부적으로 `AutoConfigurationImportSelector`를 실행한다.
- 이 클래스는 자동 설정 대상 클래스들을 메타정보에서 불러오고, 조건을 평가하여 ApplicationContext에 등록한다.

---

#### 3. 자동 설정 클래스 로딩 방식

Spring Boot는 아래 위치에 정의된 메타 정보 파일을 통해 자동 설정 클래스를 로딩한다.

- Spring Boot 2.x: `META-INF/spring.factories`  
- Spring Boot 3.x: `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`

예시:

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
...
```

이 목록의 클래스들은 조건을 만족할 경우 자동으로 Bean으로 등록된다.

---

#### 4. 조건부 설정 적용 방식

각 AutoConfiguration 클래스는 다양한 `@Conditional` 계열 애노테이션으로 감싸져 있으며,  
조건이 충족되는 경우에만 설정이 적용된다.

- `@ConditionalOnClass`: 클래스패스에 특정 클래스가 있을 경우
- `@ConditionalOnMissingBean`: 동일한 타입의 Bean이 없을 경우
- `@ConditionalOnProperty`: 특정 프로퍼티가 존재하거나 값이 일치할 경우

```java
@Configuration
@ConditionalOnClass(DataSource.class)
@EnableConfigurationProperties(DataSourceProperties.class)
public class DataSourceAutoConfiguration {
  // DataSource 관련 Bean 자동 등록
}
```

---

#### 5. 핵심 철학

> "자동 구성은 기본값일 뿐이며, 사용자의 명시적 설정이 존재하면 자동 구성은 적용되지 않는다."

- 자동 설정은 빠른 시작을 돕기 위한 기본 구성이다.
- 개발자가 직접 설정하면 그것이 우선된다.

---

#### 6. 확장과 커스터마이징

- `@AutoConfigureBefore`, `@AutoConfigureAfter`로 자동 설정 간 우선순위 조정 가능
- `@ImportAutoConfiguration`으로 특정 자동 설정만 명시적으로 가져올 수 있음
- 사용자 정의 AutoConfiguration 클래스도 만들 수 있으며, `spring.factories` 또는 `.imports` 파일에 등록하면 된다.

---

#### ✅ 요약

| 단계 | 설명 |
|------|------|
| 1 | `@EnableAutoConfiguration`으로 자동 설정 트리거 |
| 2 | `AutoConfigurationImportSelector`가 클래스 목록 로딩 |
| 3 | `.factories` 또는 `.imports`에서 자동 설정 대상 탐색 |
| 4 | `@Conditional`로 조건 평가 |
| 5 | 사용자의 명시적 설정이 있으면 자동 설정 무시 |

Spring Boot AutoConfiguration은 생산성과 유연성을 모두 제공하기 위한 핵심 메커니즘이다.
