# 어떤 예외가 발생하면 트랜잭션을 롤백하나요?


## Spring 트랜잭션 예외 처리 동작 원칙
### Checked Exception

기본적으로 트랜잭션을 롤백하지 않습니다.

Checked Exception은 컴파일 시점에 예외 처리를 강제하는 예외로, 애플리케이션이 정상 흐름 내에서 예외 가능성을 고려하여 처리할 수 있다고 간주됩니다.

따라서 이러한 예외는 롤백이 필요한 치명적인 오류로 간주되지 않습니다.

### Unchecked Exception (RuntimeException, Error)

기본적으로 트랜잭션을 롤백합니다.

이는 NullPointerException, IllegalArgumentException 등과 같이 프로그래머의 실수나 시스템적인 문제로 인한 회복 불가능한 예외로 간주되기 때문입니다.

Spring은 JDBC, JPA, Hibernate 등 하위 데이터 액세스 계층의 예외를 DataAccessException 계층으로 감싸서 모두 Unchecked 예외로 통일하여 처리합니다.

이로써 상위 계층에서 일관된 예외 처리 전략을 적용할 수 있습니다.
