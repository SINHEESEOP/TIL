# Lombok

Java 코드를 간결하게 만들어주는 Lombok 라이브러리에 대한 학습 내용입니다.

## 주요 내용

- [Lombok 완벽 가이드](./LombokGuide.md) - Lombok의 개념, 기능, 사용법 및 Spring과의 통합 방법

## Lombok 핵심 기능

- **코드 간소화**: getter, setter, constructor 등 자동 생성
- **불변 객체 처리**: `@Value` 어노테이션으로 불변 객체 생성
- **빌더 패턴**: `@Builder` 어노테이션으로 빌더 패턴 구현
- **로깅 지원**: `@Slf4j` 등의 로깅 어노테이션 제공
- **의존성 주입 지원**: `@RequiredArgsConstructor`로 생성자 주입 지원

## 추가 자료

- [Lombok 공식 사이트](https://projectlombok.org/)
- [GitHub 리포지토리](https://github.com/projectlombok/lombok)
- [Spring Boot에서 Lombok 사용하기](https://spring.io/guides/gs/spring-boot/) 