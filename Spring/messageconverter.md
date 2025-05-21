# 📘 Spring (Non-Boot) 환경에서 Jackson 자동 직렬화 설정 정리

## ✅ 개요

Spring Boot에서는 Jackson이 자동으로 설정되지만,  
**Spring MVC (비-부트 환경)**에서는 자동 직렬화를 위해  
**`MappingJackson2HttpMessageConverter`를 명시적으로 등록**해야 한다.

---

## 🧭 전제 조건

- `spring-boot-starter-web` 사용 ❌ (순수 Spring MVC)
- JSON 응답을 위한 `@ResponseBody` 또는 `@RestController` 사용
- `@EnableWebMvc` 활성화되어 있음

---

## ❗ 문제가 발생하는 시나리오

~~~java
@GetMapping("/api")
@ResponseBody
public UserDto getUser() {
    return new UserDto("홍길동", 30);
}
~~~

### 🚨 발생 가능한 현상

- 클라이언트가 `Accept: application/json`을 보냄
- 반환값이 DTO인데 JSON으로 직렬화되지 않음
- 또는 406 Not Acceptable 오류 발생

### 원인

- `MappingJackson2HttpMessageConverter`가 등록되어 있지 않음
- `@EnableWebMvc` 선언 시 Spring의 기본 메시지 컨버터 설정이 제거됨

---

## ✅ Java Config 기반 설정

~~~java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        ObjectMapper objectMapper = new ObjectMapper();
        // 예: null 제거, 날짜 포맷 설정 등
        // objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);

        MappingJackson2HttpMessageConverter jsonConverter =
                new MappingJackson2HttpMessageConverter(objectMapper);

        converters.add(jsonConverter);
    }
}
~~~

---

## ✅ XML 설정 예시

~~~xml
<mvc:annotation-driven>
    <mvc:message-converters>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
    </mvc:message-converters>
</mvc:annotation-driven>
~~~

---

## ✅ 의존성 확인 (Maven 기준)

~~~xml
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.x.x</version>
</dependency>
~~~

---

## 🧠 시야 전이: 왜 수동 등록이 필요한가?

| 이유 | 설명 |
|------|------|
| `@EnableWebMvc` 선언 | 기본 컨버터 등록이 무효화됨 |
| Spring Boot가 아님 | `ObjectMapper`, `Jackson2Converter` 자동 등록 없음 |
| Spring MVC 구조 | 모든 메시지 컨버터는 명시적으로 설정해야 함 |

---

## ✅ 설정 확인 체크리스트

| 항목 | 상태 |
|------|------|
| `@EnableWebMvc` 선언 여부 | ✅ |
| `MappingJackson2HttpMessageConverter` 직접 등록 | ✅ |
| `ObjectMapper` 생성 및 설정 (옵션) | ⚙️ |
| `Accept: application/json` 대응 가능 여부 | ✅ |
| 테스트 URL 응답 타입이 `application/json`인지 확인 | ✅ |

---

## 🔄 Gson.toJson() vs Jackson 메시지 컨버터

| 항목 | Gson.toJson (직접 호출) | Jackson 자동 직렬화 |
|------|--------------------------|----------------------|
| 반환 타입 | `String` | `Map`, DTO |
| Accept 헤더 대응 | ❌ | ✅ |
| Spring 생태계 연동 (Swagger 등) | ❌ | ✅ |
| 커스터마이징 (null 제거, 날짜 포맷 등) | 제한적 | 풍부 |
| 컨버터 등록 필요 | ❌ (직접 문자열 반환) | ✅ 수동 등록 필요 |

---

## ✅ 결론

- Spring MVC 환경에서는 JSON 직렬화를 위해 **Jackson 메시지 컨버터를 명시적으로 등록해야 한다.**
- 가장 쉬운 방법은 `WebMvcConfigurer`의 `configureMessageConverters()`를 오버라이딩하는 것
- JSON 응답이 필요하다면 **직접 `toJson()` 하지 말고 DTO를 반환하고, 컨버터에 맡기는 구조로 전환하는 것이 바람직하다.**
