# 🎯 Git 기초

## 📝 Git이란?

Git은 Linus Torvalds가 개발한 분산형 버전 관리 시스템(DVCS)입니다. 
Linux 커널 개발에서 사용하기 위해 개발되었으며, 현재는 가장 널리 사용되는 버전 관리 시스템입니다.

## 🌱 Git의 주요 특징

1. **분산 버전 관리**
   - 중앙 서버 없이도 로컬에서 버전 관리 가능
   - 네트워크 연결 없이도 작업 가능

2. **데이터 무결성**
   - SHA-1 해시를 통한 체크섬 기록
   - 모든 파일의 변경 이력 추적

3. **스냅샷 방식**
   - 델타 기반이 아닌 스냅샷 기반으로 저장
   - 빠른 브랜치 전환과 병합 가능

## 💻 기본 명령어

### 1. 저장소 생성
```bash
# 새로운 Git 저장소 생성
git init

# 원격 저장소 복제
git clone <repository-url>
```

### 2. 변경사항 관리
```bash
# 파일의 상태 확인
git status

# 변경사항을 스테이징 영역에 추가
git add <file-name>
git add .  # 모든 변경사항 추가

# 변경사항 커밋
git commit -m "커밋 메시지"

# 변경 이력 확인
git log
```

### 3. 브랜치 관리
```bash
# 브랜치 목록 확인
git branch

# 새로운 브랜치 생성
git branch <branch-name>

# 브랜치 전환
git checkout <branch-name>
git switch <branch-name>  # Git 2.23 이상

# 브랜치 생성과 전환을 동시에
git checkout -b <branch-name>
git switch -c <branch-name>  # Git 2.23 이상
```

## 🌐 원격 저장소 작업

### 1. 원격 저장소 관리
```bash
# 원격 저장소 확인
git remote -v

# 원격 저장소 추가
git remote add <name> <url>

# 원격 저장소 제거
git remote remove <name>
```

### 2. 데이터 동기화
```bash
# 원격 저장소에서 데이터 가져오기
git fetch <remote>

# 원격 저장소에서 데이터 가져와 병합하기
git pull <remote> <branch>

# 로컬 변경사항을 원격 저장소에 업로드
git push <remote> <branch>
```

## 🔄 작업 되돌리기

```bash
# 마지막 커밋 수정
git commit --amend

# 스테이징 영역에서 파일 제거
git reset HEAD <file>

# 워킹 디렉토리의 변경사항 되돌리기
git checkout -- <file>

# 특정 커밋으로 되돌리기
git reset --hard <commit-hash>
```

## 📚 학습 자료
- [Git 공식 문서](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/ko/v2)
- [Git 간편 안내서](https://rogerdudler.github.io/git-guide/index.ko.html)

## 🎓 다음 학습 내용
- [Git Flow](git-flow.md)
- [Git 브랜치 전략](branch-strategy.md) 