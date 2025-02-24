아래는 C++에서 메모리 관리에 대해 자세히 설명한 내용입니다. 각 항목별로 왜 필요한지, 어떻게 동작하는지, 그리고 간단한 코드 예시를 통해 이해할 수 있도록 정리했습니다.

---

### 1. 동적 메모리 할당

### **new와 delete**

- **왜 필요한가?**
    - 프로그램 실행 중에 필요한 만큼 메모리를 할당하거나 해제할 수 있도록 해줍니다.
    - 정적 메모리 할당(스택)은 크기가 고정되어 있고, 함수 호출 시 자동으로 할당/해제되지만, 동적 메모리는 힙(heap) 영역에서 필요할 때 할당하고, 개발자가 직접 해제해야 합니다.
- **어떻게 동작하는가?**
    - `new`는 힙에서 메모리를 할당하고, 해당 객체의 생성자를 호출한 후 포인터를 반환합니다.
    - `delete`는 포인터가 가리키는 객체의 소멸자를 호출하고, 메모리를 해제합니다.
- **예시:**
    
    ```cpp
    #include <iostream>
    using namespace std;
    
    class Sample {
    public:
        Sample() { cout << "Sample 생성" << endl; }
        ~Sample() { cout << "Sample 소멸" << endl; }
    };
    
    int main() {
        // 동적 메모리 할당: 힙에 Sample 객체를 생성
        Sample* ptr = new Sample();
    
        // 사용 후 동적 메모리 해제
        delete ptr;
    
        return 0;
    }
    ```
    

### **다차원 동적 배열 할당**

- **왜 필요한가?**
    - 배열의 크기를 런타임에 결정하거나, 다차원 배열을 동적으로 생성할 때 사용합니다.
- **어떻게 동작하는가?**
    - 2차원 배열의 경우, 먼저 행에 대한 포인터 배열을 할당한 후 각 행에 대해 동적으로 메모리를 할당합니다.
- **예시:**
    
    ```cpp
    #include <iostream>
    using namespace std;
    
    int main() {
        int rows = 3, cols = 4;
    
        // 행에 대한 포인터 배열 할당
        int** matrix = new int*[rows];
    
        // 각 행마다 열의 메모리 할당
        for (int i = 0; i < rows; ++i) {
            matrix[i] = new int[cols];
        }
    
        // 예시로 값을 채우고 출력
        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                matrix[i][j] = i * cols + j;
                cout << matrix[i][j] << " ";
            }
            cout << endl;
        }
    
        // 메모리 해제: 각 행 해제 후, 행 배열 해제
        for (int i = 0; i < rows; ++i) {
            delete[] matrix[i];
        }
        delete[] matrix;
    
        return 0;
    }
    ```
    

### 2. 스마트 포인터 (C++11 이후)

### **왜 필요한가?**

- 동적 메모리 관리 시, 수동으로 `new`와 `delete`를 사용하면 메모리 누수나 예기치 않은 버그가 발생할 수 있습니다.
- 스마트 포인터는 객체 소유권을 명확하게 관리하여, 객체가 더 이상 필요 없을 때 자동으로 메모리를 해제해줍니다.

### **주요 종류와 동작 원리**

- **std::unique_ptr**
    - 단독 소유권을 가지며, 복사할 수 없고 이동만 가능합니다.
    - 객체가 유일하게 소유되므로, 소멸 시 자동으로 메모리 해제됩니다.
- **std::shared_ptr**
    - 여러 포인터가 한 객체를 공유할 수 있으며, 참조 카운트를 통해 마지막 소유자가 해제될 때 메모리를 해제합니다.
- **std::weak_ptr**
    - `std::shared_ptr`를 보조하는 포인터로, 소유권을 가지지 않으면서 객체를 관찰할 수 있습니다.
    - 순환 참조를 방지하기 위해 사용됩니다.

### **예시:**

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Sample {
public:
    Sample() { cout << "Sample 생성" << endl; }
    ~Sample() { cout << "Sample 소멸" << endl; }
};

int main() {
    // unique_ptr 예시: 객체의 단독 소유권 관리
    unique_ptr<Sample> uptr(new Sample());
    // 또는 make_unique (C++14 이후)
    // auto uptr = make_unique<Sample>();

    // shared_ptr 예시: 객체의 공유 소유권 관리
    shared_ptr<Sample> sptr1 = make_shared<Sample>();
    {
        shared_ptr<Sample> sptr2 = sptr1;  // 참조 카운트 증가
        cout << "sptr2 생성, 참조 카운트: " << sptr1.use_count() << endl;
    } // sptr2 범위 종료, 참조 카운트 감소
    cout << "sptr2 소멸 후, 참조 카운트: " << sptr1.use_count() << endl;

    return 0;
}
```

- **설명:**
    - `unique_ptr`는 단독 소유권으로 객체를 관리하며, 소멸 시 자동으로 `delete`를 호출합니다.
    - `shared_ptr`는 복수의 소유자가 있을 경우 참조 카운트를 관리하여, 마지막 소유자가 해제되면 메모리를 자동으로 해제합니다.


### 3. 메모리 누수와 RAII

### **메모리 누수란?**

- 할당된 메모리를 해제하지 않아 프로그램이 불필요한 메모리를 계속 사용하게 되는 현상입니다.
- 이는 프로그램의 성능 저하 및 예기치 않은 동작을 유발할 수 있습니다.

### **RAII (Resource Acquisition Is Initialization)**

- **개념:**
    - 자원(메모리, 파일 핸들, 네트워크 소켓 등)을 객체의 생성 시 획득하고, 소멸 시 자동으로 해제하는 기법입니다.
- **왜 필요한가?**
    - RAII를 사용하면 객체의 수명이 끝날 때 자동으로 소멸자가 호출되어 자원이 해제되므로, 메모리 누수를 방지할 수 있습니다.
    - 스마트 포인터 역시 RAII의 원칙에 따라 설계되어, 객체의 소멸 시점에 자동으로 메모리를 해제합니다.

### **예시:**

```cpp
#include <iostream>
#include <memory>
using namespace std;

class FileHandler {
public:
    FileHandler(const char* filename) {
        // 파일 열기 (예시)
        cout << "파일 " << filename << " 열기" << endl;
    }
    ~FileHandler() {
        // 파일 닫기 (예시)
        cout << "파일 닫기" << endl;
    }
};

int main() {
    {
        // RAII: FileHandler 객체가 스코프 내에서 생성되고, 스코프를 벗어나면 자동으로 소멸되어 파일이 닫힘
        FileHandler fh("example.txt");
        // 파일 작업 수행...
    } // 여기서 fh의 소멸자가 호출되어 파일이 닫힘

    // 스마트 포인터 역시 RAII를 기반으로 함
    {
        auto sptr = make_shared<FileHandler>("example2.txt");
        // sptr를 통해 파일 작업 수행...
    } // sptr가 소멸하면서 파일 핸들이 자동으로 정리됨

    return 0;
}
```

- **설명:**
    - `FileHandler` 클래스는 생성자에서 파일을 열고, 소멸자에서 파일을 닫습니다.
    - 스코프를 벗어나면 소멸자가 자동으로 호출되어, 자원(파일 핸들)이 자동으로 정리됩니다.
    - 이는 RAII 기법의 대표적인 예시이며, 메모리 누수를 방지하는 데 매우 유용합니다.


### 종합 정리

- **동적 메모리 할당**:
    - `new`와 `delete`를 사용해 힙에서 메모리를 직접 할당/해제합니다.
    - 다차원 배열은 포인터 배열을 통해 동적으로 할당할 수 있습니다.
- **스마트 포인터**:
    - C++11부터 도입되어, 수동 메모리 관리를 줄이고 자원 누수를 방지합니다.
    - `unique_ptr`, `shared_ptr`, `weak_ptr` 등 다양한 종류가 있으며, 각각의 소유권과 참조 관리를 자동으로 처리합니다.
- **RAII (Resource Acquisition Is Initialization)**:
    - 자원 획득을 객체의 초기화(생성자)에서 수행하고, 객체 소멸 시 자동으로 자원을 해제하여 메모리 누수를 예방하는 기법입니다.
    - 스마트 포인터와 같은 도구들이 RAII 원칙에 기반해 설계되어 있습니다.