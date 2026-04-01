# RV-Linking-Lab

FYI: My Codespace Runtime for this repository :  https://crispy-adventure-5gjqxgxq6jh476r.github.dev/ 

# [실습 가이드] GitHub Codespaces를 이용한 RISC-V 개발 환경 구축

개인 랩탑에 risc-v toolchain 이 설치 된 경우는 어려운 경우 클라우드 개발 환경인 **GitHub Codespaces**를 사용하여 RISC-V 컴파일러와 링커를 실행. 
그렇지 않으면 이미 설치된 환경에서 Step 4 부터 실행. 


### **Step 1: GitHub 로그인 및 저장소(Repository) 준비**
1. [GitHub](https://github.com/)에 로그인합니다.
2. 우측 상단의 **[+]** 아이콘을 누르고 **[New repository]**를 선택합니다.
3. Repository name에 `RV-Linking-Lab`이라고 입력하고, 하단의 **[Create repository]**를 클릭합니다. (다른 설정은 건드릴 필요 없습니다.)

### **Step 2: Codespace 시작하기**
1. 생성된 저장소 화면에서 초록색 **[<> Code]** 버튼을 클릭합니다.
2. **[Codespaces]** 탭을 선택하고, **[Create codespace on main]**을 클릭합니다.
3. 잠시 기다리면 브라우저 안에 익숙한 **VS Code 화면**이 나타납니다.

### **Step 3: RISC-V 툴체인 설치**
화면 하단의 **Terminal(터미널)** 창에 다음 명령어를 한 줄씩 복사해서 붙여넣고 엔터를 누르세요. (약 30초 소요)

```bash
# 1. 패키지 목록 업데이트
sudo apt update

# 2. RISC-V 컴파일러 및 바이너리 도구 설치
sudo apt install -y gcc-riscv64-unknown-elf binutils-riscv64-unknown-elf
```

> **설치 확인:** 터미널에 `riscv64-unknown-elf-gcc --version`을 입력했을 때 버전 정보가 나오면 성공입니다!

---

### **Step 4: 실습 파일 생성 및 코드 작성**
좌측 파일 탐색기에서 새 파일을 만들어 아래 코드를 넣으세요.

1. **`main.c` 생성**
   ```c
   extern int sum(int* arr, int len);
   int array[4] = {10, 20, 30, 40};
   int length = 4;
   int result;
   int main() {
       result = sum(array, length);
       return 0;
   }
   ```
2. **`math_lib.c` 생성**
   ```c
   int sum(int* arr, int len) {
       int total = 0;
       for (int i = 0; i < len; i++) {
           total += arr[i];
       }
       return total;
   }
   ```

---

### **Step 5: 링킹(Linking) 과정 분석하기 (핵심 명령어)**
터미널에서 다음 명령어들을 실행하며 링커의 역할을 관찰.

#### **① 분할 컴파일 (오브젝트 파일 생성)**
```bash
riscv64-unknown-elf-gcc -c -march=rv32i -mabi=ilp32 main.c math_lib.c
```

#### **② 링킹 전 상태 확인 (Symbol 확인)**
```bash
riscv64-unknown-elf-nm main.o
```
* `sum` 심볼 옆에 **`U` (Undefined)**가 떠 있는지 확인. "아직 주소가 없다"는 뜻

#### **③ 최종 링킹 (실행 파일 생성)**
```bash
riscv64-unknown-elf-gcc -nostdlib -march=rv32i -mabi=ilp32 main.o math_lib.o -o final_program
```

#### **④ 링킹 완료 후 확인 (주소 재배치 확인)**
```bash
riscv64-unknown-elf-objdump -d final_program
```
* `main` 함수 안의 `jal` 명령어가 이제 `sum`의 **실제 주소**를 가리키고 있는지 확인!

---

### **💡 주의사항**
* **실습 종료 후:** Codespace는 사용하지 않을 때도 시간을 소모할 수 있습니다. 실습이 끝나면 브라우저 탭을 닫고, GitHub의 Codespaces 관리 페이지에서 **[Stop Codespace]**를 하여 종료. 

---

