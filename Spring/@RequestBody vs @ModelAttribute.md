
# @RequestBody vs @ModelAttribute

## ✅ 요약

| 항목 | @RequestBody | @ModelAttribute |
|------|--------------|-----------------|
| 사용 대상 | JSON, XML 등 요청 본문 | 폼 데이터, 쿼리 파라미터 |
| 파싱 방식 | HttpMessageConverter | WebDataBinder |
| 주로 사용하는 곳 | REST API (JSON) | 전통적인 웹 폼 |
| 바인딩 대상 | 요청 본문 → 객체 | 요청 파라미터 → 객체 |
| Content-Type 필요 | `application/json` 등 | `application/x-www-form-urlencoded` 등 |
| 유효성 검사 | `@Valid`, `@Validated` 지원 | `@Valid`, `@Validated` 지원 |

---

## 💡 동작 차이

### 📌 @RequestBody

- 요청 본문(JSON, XML 등)을 Java 객체로 변환  
- `HttpMessageConverter`를 통해 변환됨  
- 주로 REST API 개발에서 사용됨

#### 🔧 예시

```java
@PostMapping("/request-body")
public ResponseEntity<String> handleRequestBody(@RequestBody UserDto dto) {
    // JSON -> UserDto
    return ResponseEntity.ok("이름: " + dto.getName());
}
```

요청 예시:
```http
POST /request-body
Content-Type: application/json

{
  "name": "친구",
  "age": 29
}
```

---

### 📌 @ModelAttribute

- HTML 폼, 쿼리스트링 파라미터를 객체에 바인딩  
- 내부적으로는 `WebDataBinder`가 동작  
- 생략해도 기본값으로 동작 (`@ModelAttribute`는 optional)

#### 🔧 예시

```java
@GetMapping("/model-attr")
public String handleModelAttr(@ModelAttribute UserDto dto) {
    // name=친구&age=29 -> UserDto
    return "이름: " + dto.getName();
}
```

요청 예시:
```http
GET /model-attr?name=친구&age=29
```

---

## ✅ 정리 문장

- `@RequestBody`는 **요청 바디(JSON/XML)** → 객체  
- `@ModelAttribute`는 **요청 파라미터(query/form)** → 객체  
- REST API에선 `@RequestBody`,  
  전통적인 폼 전송 방식에선 `@ModelAttribute` 사용이 일반적
