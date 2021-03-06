---
layout: post
title: "fork() 의 개념과 예제"
subtitle: "fork()는 현재 실행중인 process를 복사해서 다른 process를 생성한다.<br>복사해서 생성하기 때문에, 가지고 있던 메모리등의 시스템 자원을 모두 원래의 process와 공유하게 된다."
excerpt_separator: <!--more-->
tags: linux
---

`fork()`를 사용하여 생성한 프로세스는 **부모 프로세스 <small>Parent process</small>**, 새로 생긴 프로세스는 **자식 프로세스 <small>Child process</small>** 라고 부른다.

모든 프로세스는 <small>(참고: 최상위 프로세스인 `init`는 pid 1을 가진다)</small> 생성될 때 프로세스 아이디를 부여받는다. `fork()` 함수를 이용해서 자식 프로세스를 생성할 수 있는데, 이 때 부모에게는 자식 프로세스의 pid를 반환하고, 자식에게는 0을 반환한다. 이를 이용하여 자식 프로세스에게 특정 명령을 시킬 수 있다.

<!--more-->

## 기초 예제

다음은 C로 자식 프로세스를 생성하는 예제이다:

```c
#include <stdio.h>

int main() {
  // 프로세스 번호 (Process ID; PID)가 저장될 변수 pid
  pid_t pid;

  // 자식 프로세스를 생성한다.
  // 성공시 0, 실패시 -1, 부모 프로세스이면 양수 (프로세스 ID)를 반환한다.
  pid = fork();

  if (pid < 0) {
    // pid 가 음수이면 에러가 난 것이다.

    fprintf(stderr, "fork failed!");
    return -1;

  } else if (pid == 0) {
    // pid 가 0 이면 자식 프로세스이다.
    // 자식 프로세스가 해야 할 일을 선언한다.

    printf("자식 프로세스 (# %i)", getpid());

  } else {
    // pid 가 양수이면 부모 프로세스이다.
    // 부모 프로세스가 해야 할 일을 선언한다.

    printf("부모 프로세스 (# %i)", getpid());

    // 자식 프로세스들이 모두 종료될 때 까지 기다린다.
    wait(NULL);

  }
  return 0;
}
```

### 유의사항

-   자식 프로세스 내(`pid == 0` 구간)에서 또 `fork()`를 사용하여 손자(!?) 프로세스까지 만들 수 있다.
-   부모 프로세스에서 `wait(NULL)` 함수가 불러지면 자식 프로세스가 끝날 때 까지 기다린 후에 진행한다.
-   주의: `pid` 는 변수명이지 실제 pid 를 의미하지 않는다. 따라서 `getpid()` 를 사용해야 이 함수를 부른 프로세스의 id 를 얻을 수 있다.

## 병렬 연산

메모리를 공유하기 때문에 병렬 연산도 가능하다.

```c
// for 문으로 1부터 세어올라갈 숫자
#define N               100000000

// 8개의 프로세스
#define TARGET_NUM        8

int main() {
  for (processes = 1; processes <= TARGET_NUM; processes++) {
      pid = fork();
      if (pid < 0) {
          fprintf(stderr, "Fork failed! Aborting..\\n");
          return -1;
      } else if (pid == 0) {
          // 자식 프로세스
          printf("Process %ld (%d) started.\\n", processes, getpid());
          fflush(stdout);

          // 타이머 시작, 연산, 타이머 끝, 타이머 계산
          gettimeofday(&start_time[processes-1],NULL);
          for (i = 0, x = 0; i < N/input; i++) {
              x = x + 1;
          }
          gettimeofday(&stop_time[processes-1],NULL);
          timersub(&stop_time[processes-1],
          	&start_time[processes-1],
          	&elapsed_time[processes-1]);

          // 이해하기 쉬운 메세지를 출력한다.
          printf("Process %ld (%d) total time was %f seconds. x = %lld.\n",
          			processes,
          			getpid(),
          			elapsed_time[processes-1].tv_sec+elapsed_time[processes-1].tv_usec/1000000.0, x);
          fflush(stdout);

          // 자식 프로세스는 여기까지만 하면 된다. break 문을 사용하지 않으면 자기 혼자 for 문을 반복하게 된다.
          break;
      }

  }
  // 부모 프로세스는 자식 프로세스들이 연산과 보고를 마칠 때 까지 기다린다.
  wait(NULL);
  return 0;
}
```

## 마치며

배우면서 조금 햇갈렸던 점은 프로그램 (코드) 자체가 부모 프로세스라는 개념이고, 또 프로세스 생성 시 부모와 자식간 해야할 일을 잘 구분해서 실행하게끔 하는 것이었다 (Control Flow).

직접 코드를 읽어내려가며 옆에 펜과 종이를 써서 그림을 그리면서 이해하는 것이 가장 쉬운 방법이었던 것 같다.
