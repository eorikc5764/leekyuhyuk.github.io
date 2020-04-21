---
layout  : post
title   : "[PiRTOS] 메모리 설계하기"
date    : 2020-04-15 21:54:59 +0900
category: Raspberry-Pi
---

이 글에서는 PiRTOS의 메모리를 설계해보겠습니다.

우리가 [이전 글](https://kyuhyuk.kr/article/raspberry-pi/2020/04/15/PiRTOS-Follow-The-Basics)에서 만든 ELF는 크게 세 가지로 나누어 사용합니다.

- **text 영역 :** 컴파일러가 만든 기계어가 위치하는 영역입니다.
  - 이 영역은 우리가 작성한 코드이므로 절대로 변경돼서는 안됩니다.
- **data 영역 :** 초기화된 전역변수가 위치하는 영역입니다.
  - 전역 변수를 선언할 때 초기화를 같이하면 이 위치에 입력됩니다.
- **BSS 영역 :** 초기화되지 않은 전역변수가 위치하는 영역입니다.
  - 이 영역에 위치한 전역변수들은 나중에 `0`으로 초기화됩니다.
  - 심볼과 크기 정보만 가지고 있으며, 나중에 메모리에 올라갈 때 그 크기만큼 `0`으로 채워지게 됩니다.

우선 `text` 영역을 설계해보겠습니다. Raspberry Pi 2의 메모리 용량은 1GB나 되므로 `text` 영역에 1MB를 할당하겠습니다.  
Exception Vector Table를 `text` 영역에 포함시킬 것이므로 시작 주소는 `0x00008000`입니다. (원래 ARM은 `0x00000000`에 Exception Vector Table을 설계하지만, Raspberry Pi는 `0x00008000`에 커널이 적재되므로 시작 주소를 `0x00008000`으로 설정하였습니다.) 크기를 1MB로 설정하면 끝나는 주소는 `0x00107FFF`입니다.

다음은 `data` 영역과 `BSS` 영역에 들어갈 데이터를 할당해야 합니다. 우선은 그냥 쭉 배치하도록 하겠습니다.

- USR, SYS (2MB) : `0x00108000` ~ `0x00307FFF`
- SVC (1MB) : `0x00308000` ~ `0x00407FFF`
- IRQ (1MB) : `0x00408000` ~ `0x00507FFF`
- FIQ (1MB) : `0x00508000` ~ `0x00607FFF`
- ABT (1MB) : `0x00608000` ~ `0x00707FFF`
- UND (1MB) : `0x00708000` ~ `0x00807FFF`

개별 동작 모드마다 각 1MB를 할당했습니다. USR 모드와 SYS 모드는 메모리 공간과 레지스터를 모두 공유하므로 하나로 묶었으며, 기본 동작 모드로 사용될 것이므로 2MB를 할당하였습니다.

- USR(User), SYS(System) : ARM Core의 일반적인 동작
- SVC(Supervisor Call) : SVC 명령으로 발생시키는 익셉션
- IRQ : IRQ 인터럽트가 발생했을 때
- FIQ : FIQ 인터럽트가 발생했을 때
- ABT(Abort) : 문제가 생겼을 때
- UND(Undefined Instruction) : 잘못된 명령어를 실행했을 때

RTOS에서 동작할 태스크(Task) 스택 영역도 설계해야 합니다. 태스크마다 각 1MB씩 스택 영역을 할당할 것이므로 총 64MB를 배정할 것입니다. 그 뜻은 PiRTOS의 최대 태스크 개수는 64개가 된다는 뜻입니다. 태스크 스택 영역 뒤에는 전역 변수용으로 1MB를 할당하고, 남는 공간은 동적 할당 메모리용으로 사용하겠습니다.

우리가 메모리 설계한 것을 그림으로 표현하면 아래와 같을 것입니다.

![Memory Design]({{ site.url }}/assets/image/2020-04-19-PiRTOS-Memory-Design/2020-04-19-PiRTOS-Memory-Design_1.png)  

# Exception Vector Table을 만들어보자

Exception Vector Table의 초기 코드를 작성해봅시다.  
[이전 글](https://kyuhyuk.kr/article/raspberry-pi/2020/04/15/PiRTOS-Follow-The-Basics)에서 작성한 `boot.S` 파일을 아래와 같이 수정합니다.

```gas
.text
  .code 32

  .global vector_start
  .global vector_end

  vector_start:
    LDR PC, reset_handler_addr
    LDR PC, undef_handler_addr
    LDR PC, svc_handler_addr
    LDR PC, pftch_abt_handler_addr
    LDR PC, data_abt_handler_addr
    B .
    LDR PC, irq_handler_addr
    LDR PC, fiq_handler_addr

    reset_handler_addr:     .word reset_handler
    undef_handler_addr:     .word dummy_handler
    svc_handler_addr:       .word dummy_handler
    pftch_abt_handler_addr: .word dummy_handler
    data_abt_handler_addr:  .word dummy_handler
    irq_handler_addr:       .word dummy_handler
    fiq_handler_addr:       .word dummy_handler
  vector_end:

  reset_handler:
    MOV R0, #1
    MOV R1, #2
    ADD R2, R0, R1

  dummy_handler:
    B .
.end
```

위의 코드를 보면, `LDR PC, reset_handler_addr` 부터 `LDR PC, fiq_handler_addr`까지 Exception Vector Table이 작성되어 있습니다.  
`reset_handler_addr: .word reset_handler` 부터 `fiq_handler_addr: .word dummy_handler`까지 변수를 선언해 놓았고 이 변수를 Exception Vector Table에서 사용합니다.  
`reset_handler:`가 Reset Exception Handler 입니다. 아직 제대로 된 Handler는 아니고 `R1`, `R2`에 각각 `1`과 `2`를 넣고 서로 더해 `R2`에 저장하는 코드입니다.
`dummy_handler:`는 말 그대로 Dummy Handler입니다. 그냥 무한 루프를 도는 코드입니다.

작성한 코드를 실행해봅시다!

```sh
$ make clean
$ make
$ make debug
$ make gdb
(gdb) target remote:8080
Remote debugging using :8080
0x00008000 in ?? ()
(gdb) continue
Continuing.
^C
Program received signal SIGINT, Interrupt.
0x00008048 in ?? ()
(gdb) info registers
r0             0x1	1
r1             0x2	2
r2             0x3	3
r3             0x0	0
r4             0x0	0
r5             0x0	0
r6             0x0	0
r7             0x0	0
r8             0x0	0
r9             0x0	0
r10            0x0	0
r11            0x0	0
r12            0x0	0
sp             0x0	0x0
lr             0x0	0
pc             0x8048	0x8048
cpsr           0x400001d3	1073742291
```

`continue`를 하고, Ctrl + C로 중단한 뒤, `info registers`로 레지스터의 상태를 보면 `R2`에 `3`이 들어가 있는 것을 볼 수 있습니다.

# Exception Vector Table Handler를 만들어보자

우선 Reset Exception Handler부터 만들어봅시다. Reset Exception Handler에서는 메모리 맵을 설정해 주는 작업이 들어가 있어야 합니다. 이 작업은 위에서 설계한 동작 모드별 스택 주소를 각 동작 모드의 `SP`에 설정합니다.

`include` 폴더를 생성하고, 그 안에 아래 코드를 `MemoryMap.h` 파일로 저장합니다. (이 파일은 스택 주소를 정의합니다)  
아래의 소스코드를 보면 `스택의 꼭대기 주소 = 스택의 시작 주소 + 스택의 크기 - 4`로 정의가 되어있는데, `-4`를 하는 이유는 Padding으로 4바이트를 비워두려고 넣은 것입니다. 이렇게 하면 나중에 디버깅할 때 스택과 스택 사이를 구분하는데 유용하기도 합니다.

```c
#define INST_ADDR_START     0x00008000
#define USRSYS_STACK_START  0x00108000
#define SVC_STACK_START     0x00308000
#define IRQ_STACK_START     0x00408000
#define FIQ_STACK_START     0x00508000
#define ABT_STACK_START     0x00608000
#define UND_STACK_START     0x00708000
#define TASK_STACK_START    0x00808000
#define GLOBAL_ADDR_START   0x04808000
#define DALLOC_ADDR_START   0x04908000

#define INST_MEM_SIZE       (USRSYS_STACK_START - INST_ADDR_START)
#define USRSYS_STACK_SIZE   (SVC_STACK_START - USRSYS_STACK_START)
#define SVC_STACK_SIZE      (IRQ_STACK_START - SVC_STACK_START)
#define IRQ_STACK_SIZE      (FIQ_STACK_START - IRQ_STACK_START)
#define FIQ_STACK_SIZE      (ABT_STACK_START - FIQ_STACK_START)
#define ABT_STACK_SIZE      (UND_STACK_START - ABT_STACK_START)
#define UND_STACK_SIZE      (TASK_STACK_START - UND_STACK_START)
#define TASK_STACK_SIZE     (GLOBAL_ADDR_START - TASK_STACK_START)
#define DALLOC_MEM_SIZE     (55 * 1024 * 1024)

#define USRSYS_STACK_TOP    (USRSYS_STACK_START + USRSYS_STACK_SIZE - 4)
#define SVC_STACK_TOP       (SVC_STACK_START + SVC_STACK_SIZE - 4)
#define IRQ_STACK_TOP       (IRQ_STACK_START + IRQ_STACK_SIZE - 4)
#define FIQ_STACK_TOP       (FIQ_STACK_START + FIQ_STACK_SIZE - 4)
#define ABT_STACK_TOP       (ABT_STACK_START + ABT_STACK_SIZE - 4)
#define UND_STACK_TOP       (UND_STACK_START + UND_STACK_SIZE - 4)
```

그리고 아래 코드를 `include` 폴더 안에 `ARMv6ZK.h`으로 저장합니다. (이 파일은 동작 모드 전환 값을 정의합니다)

```c
#define ARM_MODE_BIT_USR 0x10
#define ARM_MODE_BIT_FIQ 0x11
#define ARM_MODE_BIT_IRQ 0x12
#define ARM_MODE_BIT_SVC 0x13
#define ARM_MODE_BIT_ABT 0x17
#define ARM_MODE_BIT_UND 0x1B
#define ARM_MODE_BIT_SYS 0x1F
#define ARM_MODE_BIT_MON 0x16
```

이제 Header 파일을 작성하였으니, 각 동작 모드 스택을 초기화하는 Reset Exception Handler를 작성해봅시다. `boot.S`를 아래와 같이 수정합니다.

```gas
#include "ARMv6ZK.h"
#include "MemoryMap.h"

.text
  .code 32

  .global vector_start
  .global vector_end

  vector_start:
    LDR PC, reset_handler_addr
    LDR PC, undef_handler_addr
    LDR PC, svc_handler_addr
    LDR PC, pftch_abt_handler_addr
    LDR PC, data_abt_handler_addr
    B .
    LDR PC, irq_handler_addr
    LDR PC, fiq_handler_addr

    reset_handler_addr:     .word reset_handler
    undef_handler_addr:     .word dummy_handler
    svc_handler_addr:       .word dummy_handler
    pftch_abt_handler_addr: .word dummy_handler
    data_abt_handler_addr:  .word dummy_handler
    irq_handler_addr:       .word dummy_handler
    fiq_handler_addr:       .word dummy_handler
  vector_end:

  reset_handler:
    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_SVC
    MSR CPSR, R1
    LDR SP, =SVC_STACK_TOP

    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_IRQ
    MSR CPSR, R1
    LDR SP, =IRQ_STACK_TOP

    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_FIQ
    MSR CPSR, R1
    LDR SP, =FIQ_STACK_TOP

    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_ABT
    MSR CPSR, R1
    LDR SP, =ABT_STACK_TOP

    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_UND
    MSR CPSR, R1
    LDR SP, =UND_STACK_TOP

    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_SYS
    MSR CPSR, R1
    LDR SP, =USRSYS_STACK_TOP

  dummy_handler:
    B .
.end
```

`#include`로 우리가 작성한 Header 파일을 포함하고, 모든 동작 모드를 한 번씩 돌아가면서 스택 꼭대기 메모리 주소를 `SP`에 설정합니다.

```gas
MRS R0, CPSR
BIC R1, R0, #0x1F
ORR R1, R1, #동작 모드
MSR CPSR, R1
LDR SP, =스택 꼭대기 메모리 주소
```

- `MRS` 명령어를 사용하여 `CPSR` 레지스터의 내용을 `R0` 레지스터로 읽어옵니다.
- `BIC` 명령어를 사용하여 해당 비트를 Clear하고 `R1`에 넣습니다.
- `동작 모드` 비트를 `R1`에 Set하고, `R1`에 넣습니다.
- `MSR` 명령어를 사용하여 `R1` 레지스터의 내용을 CPSR에 넣습니다.
- `SP`에 `스택 꼭대기 메모리 주소`를 저장합니다.

위와 같은 5개의 명령어를 반복하는 과정입니다.

왜 굳이 시작 주소를 넣는 것이 아니라 꼭대기 주소를 계산해서 넣는지 궁금하지 않나요? 그 이유는 스택이 높은 주소에서 낮은 주소로 가는 특징을 가지고 있기 때문입니다.  
그리고 왜 SYS 동작 모드를 제일 마지막에 설정했을까요? 그 이유는 스택 설정을 끝내면 RTOS로 진입할 것인데, RTOS의 기본 동작 모드가 SYS이기 때문입니다. 이렇게 마지막에 설정함으로써 추가 설정 작업 없이 바로 SYS 모드로 다음 작업을 계속 이어갈 수 있습니다.

이제 빌드를 해볼까요? 하지만 지금 빌드를 하면 오류를 뿜어낼것입니다. `Makefile`을 아래와 같이 수정합니다.

```makefile
ARCH = armv6zk
MCPU = arm1176jzf-s

CC = arm-none-eabi-gcc
AS = arm-none-eabi-as
LD = arm-none-eabi-ld
OC = arm-none-eabi-objcopy

LINKER_SCRIPT = pirtos.ld

ASM_SRCS = $(wildcard *.S)
ASM_OBJS = $(patsubst %.S, build/%.o, $(ASM_SRCS))

INC_DIRS = include

pirtos = build/pirtos.elf
pirtos_bin = build/pirtos.bin

.PHONY: all clean run debug gdb

all: $(pirtos)

clean:
	rm -rf build

run: $(pirtos)
	qemu-system-arm -M raspi2 -kernel $(pirtos)

debug: $(pirtos)
	qemu-system-arm -M raspi2 -kernel $(pirtos) -S -gdb tcp::8080,ipv4

gdb:
	arm-none-eabi-gdb

$(pirtos): $(ASM_OBJS) $(LINKER_SCRIPT)
	$(LD) -n -T $(LINKER_SCRIPT) -nostdlib -o $(pirtos) $(ASM_OBJS)
	$(OC) -O binary $(pirtos) $(pirtos_bin)

build/%.o: %.S
	mkdir -p $(shell dirname $@)
	$(CC) -march=$(ARCH) -mcpu=$(MCPU) -I $(INC_DIRS) -c -g -o $@ $<
```

`INC_DIRS = include`를 추가하고, `$(AS) -march=$(ARCH) -mcpu=$(MCPU) -g -o $@ $<`를 `$(CC) -march=$(ARCH) -mcpu=$(MCPU) -I $(INC_DIRS) -c -g -o $@ $<`로 변경하였습니다.

이제 스택을 확인해봅시다.

```sh
$ make clean
$ make
$ make debug
$ make gdb
(gdb) target remote:8080
Remote debugging using :8080
0x00008000 in ?? ()
(gdb) file build/pirtos.elf
A program is being debugged already.
Are you sure you want to change the file? (y or n) y
Reading symbols from build/pirtos.elf...done.
(gdb) s
30	    MRS R0, CPSR
(gdb) s
31	    BIC R1, R0, #0x1F
(gdb) s
32	    ORR R1, R1, #ARM_MODE_BIT_SVC
(gdb) s
33	    MSR CPSR, R1
(gdb) s
34	    LDR SP, =SVC_STACK_TOP
(gdb) s
vector_end () at boot.S:36
36	    MRS R0, CPSR
(gdb) i r
r0             0x400001d3	1073742291
r1             0x400001d3	1073742291
r2             0x0	0
r3             0x0	0
r4             0x0	0
r5             0x0	0
r6             0x0	0
r7             0x0	0
r8             0x0	0
r9             0x0	0
r10            0x0	0
r11            0x0	0
r12            0x0	0
sp             0x407ffc	0x407ffc
lr             0x0	0
pc             0x8050	0x8050 <vector_end+20>
cpsr           0x400001d3	1073742291
```

`s` 명령에 대한 출력은 다음번에 실행할 코드 줄 번호와 코드 내용입니다. 34번째 줄까지 실행하고 나면 첫 번째 SVC 동작 모드 스택이 설정됩니다.  
우리가 설계한 메모리를 보면 SVC는 `0x00308000`부터 `0x00407FFF`까지가 메모리 주소입니다. 스택 경계에 4바이트를 비워두도록 크기를 설정했으므로 `SP`(스택 포인터)에 저장되어야 할 값은 `0x00407FFC`여야 합니다. 위의 GDB를 보면, `sp 0x407ffc  0x407ffc`입니다. `SP`(스택 포인터)의 값이 의도한 대로 잘 설정되어 있음을 확인할 수 있습니다.  
`cpsr`을 보면 `0x400001D3`입니다. 마지막 바이트를 보면 `0xD3`인데, 이 값을 2진수로 바꾸면 `11010011`이고 마지막 하위 5비트만 잘라보면 `10011`입니다. 이 숫자를 16진수로 바꾸면 SVC의 동작 모드인 `0x13`입니다.  
이어서 IRQ, FIQ, ABT, UND, SYS 모드도 동작 모드 비트와 스택 포인터의 값을 확인해봅시다.

# C언어를 사용해보자

우리가 작성한 `boot.S`에 `BL main`을 아래와 같이 추가해줍니다.

```gas
    MRS R0, CPSR
    BIC R1, R0, #0x1F
    ORR R1, R1, #ARM_MODE_BIT_SYS
    MSR CPSR, R1
    LDR SP, =USRSYS_STACK_TOP

    BL main
```

방금 추가한 `BL main`으로 어셈블리어 코드에서 C언어 코드로 진입할 수 있습니다. 이제 C언어 코드를 작성해봅시다. `Main.c`를 아래와 같이 작성합니다.

```c
#include <stdint.h>

void main(void)
{
  uint32_t* dummyAddr = (uint32_t*)(1024*1024*100);
  *dummyAddr = 0x000E34BC;
}
```

위의 소스코드는 메모리 주소 100MB(`0x6400000`)에 `0x000E34BC`라는 의미 없는 값을 기록하는 코드입니다.

C언어 코드를 추가했으니 `Makefile`도 변경되어야겠죠?

```makefile
ARCH = armv6zk
MCPU = arm1176jzf-s

CC = arm-none-eabi-gcc
AS = arm-none-eabi-as
LD = arm-none-eabi-ld
OC = arm-none-eabi-objcopy

LINKER_SCRIPT = pirtos.ld
MAP_FILE = build/pirtos.map

ASM_SRCS = $(wildcard *.S)
ASM_OBJS = $(patsubst %.S, build/%.o, $(ASM_SRCS))

C_SRCS = $(wildcard *.c)
C_OBJS = $(patsubst %.c, build/%.o, $(C_SRCS))

INC_DIRS = include

pirtos = build/pirtos.elf
pirtos_bin = build/pirtos.bin

.PHONY: all clean run debug gdb

all: $(pirtos)

clean:
	rm -rf build

run: $(pirtos)
	qemu-system-arm -M raspi2 -kernel $(pirtos)

debug: $(pirtos)
	qemu-system-arm -M raspi2 -kernel $(pirtos) -S -gdb tcp::8080,ipv4

gdb:
	arm-none-eabi-gdb

$(pirtos): $(ASM_OBJS) $(C_OBJS) $(LINKER_SCRIPT)
	$(LD) -n -T $(LINKER_SCRIPT) -nostdlib -o $(pirtos) $(ASM_OBJS) $(C_OBJS) -Map=$(MAP_FILE)
	$(OC) -O binary $(pirtos) $(pirtos_bin)

build/%.o: %.S
	mkdir -p $(shell dirname $@)
	$(CC) -march=$(ARCH) -mcpu=$(MCPU) -I $(INC_DIRS) -c -g -o $@ $<

build/%.o: $(C_SRCS)
	mkdir -p $(shell dirname $@)
	$(CC) -march=$(ARCH) -mcpu=$(MCPU) -I $(INC_DIRS) -c -g -o $@ $<
```

`Makefile`을 수정하였으면, 빌드하고 GDB로 디버깅을 해봅시다.

```sh
$ make clean
$ make
$ make debug
$ make gdb
(gdb) target remote:8080
Remote debugging using :8080
0x00008000 in ?? ()
(gdb) continue
Continuing.
^C
Program received signal SIGINT, Interrupt.
0x000080b8 in ?? ()
(gdb) x/8wx 0x6400000
0x6400000:	0x000e34bc	0x00000000	0x00000000	0x00000000
0x6400010:	0x00000000	0x00000000	0x00000000	0x00000000
(gdb)
```

`x/8wx 0x6400000` 명령은 `0x6400000` 메모리 주소로부터 8개를 4바이트씩 16진수로 값을 출력하는 명령입니다.
`0x6400000` 메모리 주소의 값을 보면 `0x000E34BC`가 잘 기록되어 있음을 확인할 수 있습니다.

`continue` 명령어를 사용했는데 왜 프로그램이 종료가 안될까요? 우리가 작성한 코드는 `main()` 함수를 실행하고 나면 다시 `reset_handler`로 돌아가서 무한 루프를 돌게 됩니다. 그래서 `continue` 명령어를 사용해도 프로그램이 종료되지 않는 것입니다.
