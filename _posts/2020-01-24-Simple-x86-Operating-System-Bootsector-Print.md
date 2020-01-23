---
layout  : post
title   : "[Simple x86 Operating System] Bootsector Print"
date    : 2020-01-24 01:48:55 +0900
category: Simple-x86-Operating-System
---

[이전 글]({{ site.url }}/article/simple-x86-operating-system/2020/01/22/Simple-x86-Operating-System-Bootsector-Barebones)에서 만든 무한 루프 부트 섹터를 약간 개선하고 화면에 글자를 출력할 것입니다. 이를 위해 인터럽트(Interrupt)를 발생시킬 것입니다.

이 예제에서는 "Hello" 단어의 각 문자를 `al` 레지스터(`ax`의 하위 부분), `0x0E`를 `ah` 레지스터(`ax`의 상위 부분)에 기록하고 비디오 서비스의 일반적인 인터럽트인 인터럽트 `0x10`을 발생시킬 것입니다.

`ah`의 `0x0E`는 실행하려는 기능이 'tty 모드에서 `al`의 내용을 쓰는 것'이라는 비디오 인터럽트를 알려줍니다.

새로운 부트 섹터(`boot_sect_hello.asm`)는 다음과 같습니다:

```nasm
mov ah, 0x0e ; tty mode
mov al, 'H'
int 0x10
mov al, 'e'
int 0x10
mov al, 'l'
int 0x10
int 0x10 ; 'l'이 al에 있기 때문에 int 0x10으로 한 번 더 호출합니다.
mov al, 'o'
int 0x10

jmp $ ; 현재 주소로 이동합니다. = 무한 루프

; 이전 코드의 크기에서 510까지 0으로 채웁니다.
times 510 - ($-$$) db 0

; Magic number
dw 0xaa55
```

`nasm -fbin boot_sect_hello.asm -o boot_sect_hello.bin` 명령을 사용하여 컴파일하고, `qemu-system-i386 boot_sect_hello.bin`로 실행해봅시다.

![boot_sect_simple.bin QEMU]({{ site.url }}/assets/image/2020-01-24-Simple-x86-Operating-System-Bootsector-Print/2020-01-24-Simple-x86-Operating-System-Bootsector-Print_1.png)
