# 🌿 Git

## 📝 개요

Git은 분산 버전 관리 시스템(DVCS)으로, 소프트웨어 개발에서 소스 코드를 효과적으로 관리할 수 있게 해주는 도구입니다.

## 📚 학습 내용

### 1. [Git 기초](basics.md)
- Git의 기본 개념
- 저장소 생성 및 초기화
- 기본 명령어 (add, commit, push, pull)
- 브랜치 관리

### 2. [Git Flow](git-flow.md)
- Git Flow 개념
- 브랜치 전략
- 워크플로우 예시
- 실무 적용 방법

### 3. [Git 브랜치 전략](branch-strategy.md)
- Git Flow vs GitHub Flow
- 브랜치 네이밍 컨벤션
- 머지 전략
- 코드 리뷰 프로세스

## 🔧 자주 사용하는 Git 명령어

```bash
# 저장소 초기화
git init

# 원격 저장소 복제
git clone [url]

# 변경사항 스테이징
git add [file]

# 변경사항 커밋
git commit -m "[message]"

# 원격 저장소로 푸시
git push origin [branch]

# 원격 저장소에서 풀
git pull origin [branch]

# 브랜치 생성 및 전환
git checkout -b [branch-name]
```

## 📌 유용한 Git 설정

```bash
# 사용자 정보 설정
git config --global user.name "[name]"
git config --global user.email "[email]"

# 기본 브랜치 이름 설정
git config --global init.defaultBranch main

# 자동 줄바꿈 설정
git config --global core.autocrlf true
```

## 🔍 참고 자료
- [Git 공식 문서](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/ko/v2)
- [Atlassian Git Tutorial](https://www.atlassian.com/git/tutorials) 