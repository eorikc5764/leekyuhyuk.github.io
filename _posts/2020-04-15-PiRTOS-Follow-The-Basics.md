---
layout  : post
title   : "[PiRTOS] 기초적인 것부터 따라 하기"
date    : 2020-04-15 21:54:59 +0900
category: Raspberry-Pi
---

이 글에서는 Cross Compiler를 사용하는 것과 GDB를 사용하는 것을 설명하겠습니다.

# Raspberry Pi Booting Flow

ARM Core에 전원이 인가되면, Reset Vector(`0x00000000`)에서 32비트를 읽어 명령을 실행합니다.  
하지만 Raspberry Pi는 조금 다릅니다.  
Raspberry Pi의 전원이 켜지면 ARM CPU가 중지되고 GPU가 실행됩니다. GPU는 ROM에서 부트로더를 로드하고 실행합니다. 그리고 SD카드를 찾아 `bootcode.bin`을 로드합니다. bootcode는 `config.txt`와 `cmdline.txt`를 처리하는 `start.elf` 펌웨어를 로드합니다. `start.elf`는 `kernel*.img`를 로드하고 해당 커널 이미지를 실행하는 ARM CPU가 실행됩니다.  
ARM 모드 간을 전환하려면 `kernel.img` 파일의 이름을 변경해야 합니다. `kernel7.img`로 이름을 바꾸면 AArch32 모드 (ARMv7)에서 실행됩니다. AArch64 모드(ARMv8)의 경우 `kernel8.img`로 이름을 변경해야 합니다.

> config.txt에 있는 kernel 항목을 변경하여 원하는 파일을 커널 이미지로 실행하게 설정할 수 있습니다.<br>**예)** kernel=kernel.bin

커널은 AArch32(32비트) 기준으로 `0x8000`에서 시작됩니다.

>AArch64(64비트)는 `0x80000`에서 시작 됩니다.

우리가 해야 할 일은 메모리 주소 `0x8000`에 명령어를 넣어주는 것입니다.

# 간단한 어셈블리 프로그램 작성해보기

`boot.S` 파일을 아래와 같이 작성해봅니다.

```gas
.text
  .code 32

  .global vector_start
  .global vector_end

  vector_start:
    MOV R0, R1
  vector_end:
    .space 1024, 0
.end
```

- `.text`는 `.end`가 나올 때까지의 모든 코드가 text 섹션이라는 의미입니다.
  - 여기서 말하는 'text 섹션'은 컴파일러가 만든 기계어가 위치하는 섹션입니다.
- `.code 32`는 명령어의 크기가 32비트라는 뜻입니다.
- `.global`은 C언어의 `extern`과 같습니다.
  - `vector_start`와 `vector_end`의 주소 정보를 다른 파일에서 심볼로 읽을 수 있게 해줍니다.
- 7번, 9번 줄에 있는 `vector_start:`와 `vector_end:`는 레이블을 선언한 것입니다.
- `MOV R0, R1`은 `R1`의 값을 `R0`에 넣으라는 뜻입니다.
  - ARM Assembly에 대해 더 알고 싶다면, [ARM Assembly 기초](https://kyuhyuk.kr/article/raspberry-pi/2019/05/15/ARM-Assembly)을 읽어보시기 바랍니다.
- `.space 1024, 0`는 해당 위치부터 1024 바이트를 0으로 채우라는 명령입니다.
- `.end`는 text 섹션이 끝났음을 알리는 지시어 입니다.

아마 바이너리가 생성되면, 다음과 같이 생성될 것입니다.

```
메모리 주소
0000 0000 MOV R0, R1에 해당하는 기계어
0000 0004 00000000
... 생략 ...
0000 0400 00000000
```

이제 `boot.S` 파일을 컴파일 해봅시다.

```sh
$ arm-none-eabi-as -march=armv7-a -mcpu=arm1176jzf-s -o boot.o boot.S
$ arm-none-eabi-objcopy -O binary boot.o boot.bin
```

첫 번째 명령어는 `arm-none-eabi-as`를 사용하여 어셈블리어 소스 코드를 컴파일 하는 명령입니다. Raspberry Pi 2가 사용하는 ARM Core가 'arm1176jzf-s'이라서 아키텍처는 'armv7-a'로 설정하고, CPU는 'arm1176jzf-s'로 설정했습니다. 컴파일이 완료되면 `boot.o`라는 오브젝트 파일이 생성되고, 두 번째 명령어인 `arm-none-eabi-objcopy` 명령으로 바이너리만 추출합니다.

이제 `hexdump` 명령으로 `boot.bin` 바이너리의 내용을 확인해봅시다.

```
$ hexdump boot.bin
0000000 0001 e1a0 0000 0000 0000 0000 0000 0000
0000010 0000 0000 0000 0000 0000 0000 0000 0000
*
0000404
```

위에 나온 `0001 e1a0` 값은 `MOV R0, R1`의 기계어입니다. 그리고 `0x00000400`까지 `0`으로 쭉 채워져있습니다. (`*`는 계속 반복되는 값을 표시하지 않는 일종의 생략 표시입니다.)

# 부팅할 수 있는 실행 파일 만들기

QEMU에서 부팅하려면 바이너리 파일이 ELF 파일 형식이어야 합니다.
위에서 `arm-none-eabi-as`로 생성한 `boot.o`도 ELF 파일입니다.

```sh
$ file boot.o
boot.o: ELF 32-bit LSB relocatable, ARM, EABI5 version 1 (SYSV), not stripped
```

ELF 파일을 만들려면 링커(Linker)를 사용해야 합니다. 링커는 여러 오브젝트 파일을 묶어서 하나의 실행 파일로 만듭니다.

링커가 동작하려면 링커 스크립트가 필요합니다. 이 링커 스트립트는 링커에 주는 정보입니다.  
아래의 내용을 `pirtos.ld`에 작성해봅시다.

```
ENTRY(vector_start)
SECTIONS
{
    . = 0x8000;

    .text :
    {
      *(vector_start)
      *(.text .rodata)
    }

    .data :
    {
      *(.data)
    }

    .bss :
    {
      *(.bss)
    }
}
```

- `ENTRY` 지시어는 시작 위치의 심볼을 지정합니다.
- `SECTIONS` 지시어는 섹션(`text`, `.data`, `bss`) 배치 설정 정보를 가지고 있음을 알려줍니다.
- 4번째 줄의 `. = 0x8000;`는 첫 번째 섹션이 메모리 주소 `0x8000`에 위치한다는 것을 알려줍니다.
- `.text`는 `text` 섹션의 배치 순서를 지정합니다.
  - 메모리 주소 `0x8000`에 리셋 벡터가 위치해야 하므로, `vector_start` 심볼이 먼저 나오고, 그다음에 `.text` 섹션이 나오게 작성하였습니다.

`pirtos.ld`를 모두 작성하였으면, 아래 명령어로 실행 파일을 만들어봅시다.

```
$ arm-none-eabi-ld -n -T pirtos.ld -nostdlib -o pirtos.elf boot.o
```

- `-n` : 링커에 섹션의 정렬을 자동으로 맞추지 않게 합니다.
- `-T` : 링커 스크립트 파일을 지정합니다.
- `-nostdlib` : 링커가 자동으로 표준 라이브러리를 링크하지 않게 설정합니다.

위의 명령어가 실행되면 `pirtos.elf` 파일이 생성됩니다. `pirtos.elf` 파일의 내부가 어떻게 되어있는지 출력해봅시다.

```sh
$ arm-none-eabi-objdump -D pirtos.elf

pirtos.elf:     file format elf32-littlearm


Disassembly of section .text:

00008000 <vector_start>:
    8000:	e1a00001 	mov	r0, r1

00008004 <vector_end>:
	...
```

`vector_start`가 메모리 주소 `0x00008000`에 잘 배치되어 있고, 디스어셈블한 명령을 보면, `mov r0, r1`이 있음을 확인할 수 있습니다. 여기서 `mov r0, r1`의 기계어는 `0xE1A00001`입니다. 이 값을 기억해둡시다.

# QEMU에서 부팅하기

```sh
$ qemu-system-arm -M raspi2 -kernel pirtos.elf -S -gdb tcp::8080,ipv4
```

- `-M` : ARM Machine을 지정합니다. 여기서는 Raspberry Pi 2를 지정했습니다.
- `-kernel` : 커널로 로드될 ELF 파일을 지정합니다.
- `-S` : QEMU가 실행되자마자 바로 일시정지 되도록 합니다.
- `-gdb tcp::8080,ipv4` : GDB와 연결하기 위해 소켓 포트를 `8080`으로 설정합니다.
  - 여기서 `-S` 옵션과 `-gdb` 옵션을 같이 쓰는 이유는 GDB로 디버깅을 하기 위해 같이 사용합니다.

위의 명령어를 입력하면 QEMU가 실행되면서 검정 화면이 출력될 것입니다. 이제 GDB로 디버깅을 해봅시다.

아래의 명령어를 사용하여, QEMU와 GDB를 연결합니다.

```sh
$ arm-none-eabi-gdb
(gdb) target remote:8080
```

그리고, `x/4bx`를 입력하여 `0x8000`의 메모리 주소에서 4바이트를 출력합니다.
여기서 `x` 명령어는 주어진 주소 내의 메모리 값이 무엇인지 확인하는 용도로 사용됩니다.

```sh
(gdb) x/4bx 0x8000
0x8000 <vector_start>:	0x01	0x00	0xa0	0xe1
```

여기서 나온 `0x01`, `0x00`, `0xa0`, `0xe1`를 4바이트로 묶어서 표현하면 `0xE1A00001`입니다. 위에서 `mov r0, r1`의 기계어를 확인한 값과 같습니다. 이건 `pirtos.elf` 파일에 있는 코드가 QEMU의 메모리로 제대로 올라갔다는 뜻입니다.

# 레지스터 값 확인하기

`boot.S` 파일을 아래와 같이 수정해봅니다.

```gas
.text
  .code 32

  .global vector_start
  .global vector_end

  vector_start:
    MOV R0, #1
    MOV R1, #2
    ADD R2, R0, R1
  vector_end:
    .space 1024, 0
.end
```

위의 소스코드는 `R0` 레지스터에 `1`을 넣고, `R1` 레지스터에 `2`를 넣습니다. 그리고 `R0`과 `R1` 레지스터의 값을 더한 것을 `R2` 레지스터에 넣는 간단한 소스코드입니다.  

```sh
$ arm-none-eabi-as -march=armv7-a -mcpu=arm1176jzf-s -g -o boot.o boot.S
$ arm-none-eabi-ld -n -T pirtos.ld -nostdlib -o pirtos.elf boot.o
```

위의 명령어로 `pirtos.elf`를 생성합니다. 여기서 `-g` 옵션이 추가되었는데 `-g` 옵션은 디버깅 심볼을 실행 파일에 포함시키는 옵션입니다. GDB로 디버깅을 할 때 필요한 옵션입니다.

```sh
$ qemu-system-arm -M raspi2 -kernel pirtos.elf -S -gdb tcp::8080,ipv4
$ arm-none-eabi-gdb
(gdb) target remote:8080
Remote debugging using :8080
0x00008000 in ?? ()
(gdb) file pirtos.elf
A program is being debugged already.
Are you sure you want to change the file? (y or n) y
Reading symbols from pirtos.elf...done.
```

GDB에서 `target` 명령으로 QEMU 디버깅 소켓과 연결한 다음 `pirtos.elf`에 포함되어 있는 디버깅 심볼을 `file` 명령으로 읽어옵니다.  
디버깅 심볼을 제대로 읽었는지 `list` 명령을 사용하여 확인할 수 있습니다.

```sh
(gdb) list
1	.text
2	  .code 32
3
4	  .global vector_start
5	  .global vector_end
6
7	  vector_start:
8	    MOV R0, #1
9	    MOV R1, #2
10	    ADD R2, R0, R1
```

지금 QEMU는 실행 파일을 한 줄도 실행하지 않았기 때문에 레지스터를 읽어보면 레지스터에 아무런 정보도 없을 것입니다.

```sh
(gdb) info registers
r0             0x0	0
r1             0x0	0
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
sp             0x0	0x0
lr             0x0	0
pc             0x8000	0x8000 <vector_start>
cpsr           0x400001d3	1073742291
```

그러면 이 상태에서 첫 번째 명령어를 실행하고 나면, `R0`에 `1`이 입력될 것입니다.

```sh
(gdb) step
vector_start () at boot.S:9
9	    MOV R1, #2
(gdb) info registers
r0             0x1	1
r1             0x0	0
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
sp             0x0	0x0
lr             0x0	0
pc             0x8004	0x8004 <vector_start+4>
cpsr           0x400001d3	1073742291
```

예상대로 `R0` 레지스터에 `1`이 저장되어 있는 것을 확인할 수 있습니다.  
`step`과 `info registers`를 입력하면서, 레지스터의 값이 변하는 것을 봐봅시다.

> GDB에서 `step`은 `s`, `info registers`는 `i r`로 줄여서 사용할 수 있습니다.

```sh
(gdb) s
10	    ADD R2, R0, R1
(gdb) i r
r0             0x1	1
r1             0x2	2
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
sp             0x0	0x0
lr             0x0	0
pc             0x8008	0x8008 <vector_start+8>
cpsr           0x400001d3	1073742291
(gdb) s
0x0000800c in vector_end () at boot.S:10
10	    ADD R2, R0, R1
(gdb) i r
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
pc             0x800c	0x800c <vector_end>
cpsr           0x400001d3	1073742291
```

이 과정을 통하여 우리가 작성한 대로 코드가 작동하고 레지스터의 내용이 변경되는 것을 확인하였습니다.

# Makefile 만들기

`pirtos.elf` 파일을 빌드 하기 위해서는 지금까지 위에서 설명했던 `arm-none-eabi-as`로 컴파일하고, `arm-none-eabi-ld`로 링킹을 해야 합니다. 매번 이런 행위를 하는 것은 귀찮기도 하고 시간도 소요됩니다.  
이런 걸 자동화하기 위해 우리는 `Makefile`을 만들 것입니다. `Makefile`만 만들면 `make` 명령어로 한 번에 빌드 할 수 있습니다.

아래의 내용을 `Makefile`로 저장합니다.

```makefile
ARCH = armv7-a
MCPU = arm1176jzf-s

CC = arm-none-eabi-gcc
AS = arm-none-eabi-as
LD = arm-none-eabi-ld
OC = arm-none-eabi-objcopy

LINKER_SCRIPT = pirtos.ld

ASM_SRCS = $(wildcard *.S)
ASM_OBJS = $(patsubst %.S, build/%.o, $(ASM_SRCS))

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
	$(AS) -march=$(ARCH) -mcpu=$(MCPU) -g -o $@ $<
```

`make` 또는 `make all`로 `pirtos.elf`를 빌드 할 수 있으며, `make clean`으로 생성된 파일들을 지울 수도 있습니다.  
`make debug`는 QEMU를 디버깅 용도로 사용할 수 있게 해줍니다.
