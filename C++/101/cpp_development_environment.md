# OS 별 추천 환경 구축

[Window](./Window.md)

# C++ 개발 환경을 구축하는 데 필요한 주요 항목

## 1. 컴파일러 선택

C++ 코드를 실제 실행 파일로 변환하기 위해 컴파일러가 필요합니다. 대표적인 컴파일러는 다음과 같습니다:

- **GCC (GNU Compiler Collection)**
    - 다양한 운영체제(Linux, macOS, Windows(WSL))에서 사용 가능
- **Clang**
    - 빠른 컴파일과 친숙한 오류 메시지를 제공하며, macOS와 Linux에서 주로 사용
- **MSVC (Microsoft Visual C++)**
    - Windows 환경에서 Visual Studio와 함께 사용되며, 강력한 디버깅 도구 제공

## 2. 통합 개발 환경(IDE) 또는 텍스트 에디터 선택

편리한 코드 작성, 빌드, 디버깅을 위해 IDE나 에디터를 선택하는 것이 좋습니다.

- **IDE**
    - **Visual Studio** (Windows): MSVC 기반, 강력한 디버깅 기능과 프로젝트 관리 도구 제공
    - **CLion** (Cross-platform): JetBrains에서 제공하며, CMake 기반 프로젝트 관리
    - **Code::Blocks**: 가볍고 사용하기 쉬운 IDE
- **텍스트 에디터**
    - **Visual Studio Code**: 확장 기능을 통해 C++ 개발에 최적화 가능 (예: C/C++ Extension by Microsoft)
    - **Sublime Text** 또는 **Atom**: 플러그인을 추가하여 C++ 개발 환경 구축 가능

## 3. 빌드 도구 및 프로젝트 관리

C++ 프로젝트가 커지면 빌드 도구와 프로젝트 관리 시스템이 필요합니다.

- **CMake**
    - 플랫폼 독립적인 빌드 시스템으로, 여러 IDE와 연동하기 좋습니다.
- **Make**
    - Linux/Unix 환경에서 많이 사용되는 빌드 도구입니다.
- **MSBuild**
    - Visual Studio 환경에서 사용됩니다.

## 4. 디버깅 및 기타 유용한 도구

- **디버거**
    - **GDB (GNU Debugger)**: Linux, macOS, Windows(WSL)에서 사용 가능
    - **LLDB**: Clang 기반 디버거, macOS에서 주로 사용
    - **Visual Studio Debugger**: Visual Studio 내장 디버거로, 강력한 기능 제공
- **버전 관리 시스템**
    - **Git**: 소스 코드 버전 관리를 위해 필수적이며, GitHub, GitLab과 같은 원격 저장소와 연동 가능

## 5. 개발 환경 구축 단계

1. **컴파일러 설치**
    - **Windows**: Visual Studio를 설치하거나, MinGW 또는 Cygwin을 통해 GCC 설치
    - **macOS**: Xcode Command Line Tools 설치 (터미널에서 `xcode-select --install`)
    - **Linux**: 배포판의 패키지 매니저(예: apt, yum)를 사용하여 GCC/Clang 설치
2. **IDE/에디터 설치 및 설정**
    - 원하는 IDE(Visual Studio, CLion 등)나 에디터(Visual Studio Code 등)를 설치하고, C++ 확장 및 플러그인을 추가
3. **프로젝트 생성 및 설정**
    - 간단한 "Hello, World!" 프로젝트를 생성하여, 컴파일 및 실행 환경이 제대로 동작하는지 확인합니다.
    - CMake를 사용하는 경우, 기본 `CMakeLists.txt` 파일을 생성해 프로젝트 빌드 테스트
4. **디버깅 도구 설치 및 연동**
    - IDE 내 디버거나 별도의 디버깅 도구(GDB, LLDB)를 설치 및 설정하여, 코드 디버깅이 가능하도록 구성
5. **버전 관리(Git) 설정**
    - Git을 설치하고, 코드 저장소를 초기화하여 버전 관리에 익숙해지도록 합니다.

