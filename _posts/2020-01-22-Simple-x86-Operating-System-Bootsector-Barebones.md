---
layout  : post
title   : "[Simple x86 Operating System] Bootsector Barebone"
date    : 2020-01-22 23:58:13 +0900
category: Simple-x86-Operating-System
---

# 개발 환경 설정

개발 환경은 리눅스(Ubuntu 18.04)에서 진행합니다.  
`sudo apt install qemu-system-x86 nasm` 명령어로 `qemu-system-x86`과 `nasm`을 설치합니다.

# 이론

컴퓨터가 부팅 될 때 BIOS는 OS를 로드하는 방법을 모르므로 해당 작업을 부팅 섹터에 맡깁니다. 따라서 부팅 섹터는 알려진 표준 위치에 배치해야 합니다. 이 위치는 디스크의 첫 번째 섹터이며, 512 바이트가 필요합니다.

'디스크가 부팅 가능한지' 확인하기 위해 BIOS는 부팅 섹터의 `511`, `512` 바이트가 `0xAA55`인지 확인합니다.

아래는 간단한 부팅 섹터입니다.

```
EB FE 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[ 생략 ]
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 AA
```

부팅 섹터는 위와 같이 16비트 값 `0xAA55`(x86은 Little-Endian입니다)로 끝납니다. 처음 2바이트는 무한 점프를 수행합니다.

# 가장 간단한 부트 섹터

Hex Editor으로 512 바이트를 작성하거나, 매우 간단한 어셈블러 코드를 작성하여 간단한 부트 섹터를 만들 수 있습니다.

아래의 소스 코드를 `boot_sect_simple.asm`로 저장합니다.

```nasm
; 무한 루프 (EB FE)
loop:
    jmp loop

; 이전 코드의 크기에서 510까지 0으로 채웁니다.
times 510-($-$$) db 0

; Magic number
dw 0xaa55
```

`nasm -f bin boot_sect_simple.asm -o boot_sect_simple.bin` 명령을 사용하여 컴파일 할 수 있습니다.

아래는 Hex Editor으로 `boot_sect_simple.bin` 파일을 열어본 것입니다. `511`, `512` 바이트가 `0xAA55`인 것을 확인할 수 있습니다.  
![boot_sect_simple.bin Hex Editor]({{ site.url }}/assets/image/2020-01-22-Simple-x86-Operating-System-Bootsector-Barebones/2020-01-22-Simple-x86-Operating-System-Bootsector-Barebones_1.png)

`qemu-system-i386 boot_sect_simple.bin` 명령으로 실행해볼 수 있습니다.
아마 아직까지는 "Booting from Hard Disk..."이라는 창이 열리고 아무것도 표시되지 않을 것입니다.

![boot_sect_simple.bin QEMU]({{ site.url }}/assets/image/2020-01-22-Simple-x86-Operating-System-Bootsector-Barebones/2020-01-22-Simple-x86-Operating-System-Bootsector-Barebones_2.png)
