## 기본 요약

|표현|설명|처리 시점|처리 엔진|
|---|---|---|---|
|`${...}`|설정이 `.yml` 또는 `.properties`에 있을 경우|초기 프로퍼티 바인딩 시|PropertySourcesPlaceholderConfigurer 또는 Environment|
|`#{...}`|계산식, Bean/메서드 호출, 조건 등이 필요한 경우|런타임 시점|SpEL(ExpressionParser)|

---

## 사용방법

### 표현 1: `${...}` - Property Placeholder
- `.properties`, `.yml`, system env 같은 설정값을 바인딩할 때 사용
- 타입 세이프에 안전
- 항상 static
```
# application.yml
my:
  app:
    name: spring-app
```
```
@Value("${my.app.name}")
private String appName;
```

### 표현 2: `#{...}` - SpEL (Spring Expression Language)
- Bean의 메서드 호출, 조건 및 수식 계산 가능
- runtime 시점에 ExpressionParser가 해석
- 복잡하고 다양한 표현 가능
```
@Value("#{2 * 3}")
@Value("#{myBean.getValue()}")
@Value("#{systemProperties['user.name']}")
```
---
## Bean·Properties 관련 규칙
- `@Value("${a.b.c}")` : application.yml/외부 설정값에 있는 경우만 가능
- `@Value("#{myProps['a.b.c']}")` : `myProps` Bean 또는 Map/Properties 타입의 key-value 접근 가능

---
## 어떤 경우에 무엇을 쓰는가?

|                            |                  |     |
| -------------------------- | ---------------- | --- |
| 목적                         | 우선 사용 방식         |     |
| 설정 바인딩                     | `${...}`         |     |
| Bean의 함수, 계산, 변수 조건        | `#{...}`         |     |
| Bean에서 Properties 값을 찾는 경우 | `#{bean['key']}` |     |
| 단순 값 바인딩만 필요한 경우           | `${...}`         |     |

---
## 전환 가능성
- 기존 XML 설정의 `<entry key="...">...</entry>` 형태는 Java Config에서 `Properties` Bean으로 변환 가능
- 이 경우 SpEL (`@Value("#{admprp['a.b.c']}")`) 방식으로 접근 가능
- Spring Boot의 `${}`는 **Environment 설정값만 접근 가능**, Bean 내부 값은 접근 불가
    

---
### 간단 정리
- 특정 Bean의 Map 또는 Properties에서 값을 가져올 때: `#{bean['key']}`
- application.yml/가이드에 있는 설정: `${...}`
- Spring Boot에서는 `${}` 방식이 가장 확장성 높고 권장됨