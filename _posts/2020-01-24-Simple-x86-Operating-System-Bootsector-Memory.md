---
layout  : post
title   : "[Simple x86 Operating System] Bootsector Memory"
date    : 2020-01-24 02:22:07 +0900
category: Simple-x86-Operating-System
---

이 글의 목표는 부트 섹터가 저장된 위치를 배우는 것입니다.

![boot_sect_simple.bin QEMU]({{ site.url }}/assets/image/2020-01-24-Simple-x86-Operating-System-Bootsector-Memory/2020-01-24-Simple-x86-Operating-System-Bootsector-Memory_1.png)

위의 그림을 보면, BIOS가 부트 섹터를 `0x7C00`에 배치하는 것을 알 수 있습니다.

화면에 'X'를 출력하려고 합니다. 우리는 4가지 방법을 시도하고, 어떤 방법이 효과가 있는지와 그 이유를 알아봅시다.

`boot_sect_memory.asm`를 살펴봅시다.

```nasm
mov ah, 0x0e

; attempt 1
; the_secret의 메모리 주소 (예 : 포인터)를 출력하려고 하므로 실패합니다.
; 이것은 실제 내용이 아닙니다.
mov al, "1"
int 0x10
mov al, the_secret
int 0x10

; attempt 2
; 올바른 접근 방법인 the_secret의 메모리 주소를 인쇄하려고 시도합니다.
; 그러나, BIOS는 부트 섹터 바이너리를 0x7C00에 놓습니다.
; 미리 Padding을 추가해야 합니다. 우리는 attempt 3에서 Padding을 추가할 것입니다.
mov al, "2"
int 0x10
mov al, [the_secret]
int 0x10

; attempt 3
; BIOS 시작 오프셋 0x7C00을 X의 메모리 주소에 추가합니다.
; 그런 다음 해당 포인터의 내용을 역 참조합니다.
; 'mov al, [ax]'는 올바른 방법이 아니기 때문에 다른 레지스터인 'bx'의 도움이 필요합니다.
; 레지스터는 동일한 명령의 소스 및 대상으로 사용할 수 없습니다.
mov al, "3"
int 0x10
mov bx, the_secret
add bx, 0x7c00
mov al, [bx]
int 0x10

; attempt 4
; 'X'가 바이너리의 0x2D에 저장되어 있다는 것을 알기 때문에 바로 가기를 시도합니다.
; 이 방법은 좋아 보이지만 코드를 변경할 때마다 레이블 오프셋을 다시 계산해야 하기 때문에 비효율적입니다.
mov al, "4"
int 0x10
mov al, [0x7c2d]
int 0x10

jmp $ ; 무한 루프

the_secret:
    ; ASCII 코드 0x58('X')는 Zero-Padding 직전에 저장됩니다.
    ; 이 코드는 0x2D에 위치합니다. ('xxd file.bin'를 사용하여 확인 가능합니다)
    db "X"

; Zero-Padding과 Magic number
times 510-($-$$) db 0
dw 0xaa55
```

먼저, 'X'를 데이터로 정의하고 레이블을 지정합니다.

```nasm
the_secret:
    db "X"
```

위의 코드에서는 여러 가지 방법으로 `the_secret`에 접근하려고 합니다.

1. `mov al, the_secret`
2. `mov al, [the_secret]`
3. `mov al, the_secret + 0x7C00`
4. `mov al, 2d + 0x7C00`, 여기서 `2d`는 바이너리에서 'X'의 실제 위치입니다.

코드를 보고 주석을 읽으면 이해가 될 것입니다.

코드를 컴파일하고 실행하면, `1[2¢3X4X`와 비슷한 문자열을 볼 수 있습니다. 여기서 `1`과 `2` 다음에 나오는 문제는 임의의 문자입니다.

명령어를 추가하거나 제거하는 경우, 바이트 수를 계산하여 'X'의 새 오프셋을 계산하고 `0x2D`를 새 오프셋으로 교체해야 합니다.

# 글로벌 오프셋 (Global Offset)

`0x7C00`을 어디에나 오프셋하는 것은 매우 불편하므로 어셈블러는 `org` 명령을 사용하여 모든 메모리 위치에 대해 '전역 오프셋'을 정의 할 수 있습니다.

```nasm
[org 0x7c00]
```

`boot_sect_memory_org.asm`를 살펴봅시다.

```nasm
[org 0x7c00]
mov ah, 0x0e

; attempt 1
; 포인터를 가리키고 있지만 가리키는 게 데이터가 아니기 때문에 'org'에 관계없이 실패합니다.
mov al, "1"
int 0x10
mov al, the_secret
int 0x10

; attempt 2
; 'org'의 메모리 오프셋 문제를 해결 한 후 이것이 정답입니다.
mov al, "2"
int 0x10
mov al, [the_secret]
int 0x10

; attempt 3
; 0x7C00을 두 번 추가하므로 작동하지 않습니다.
mov al, "3"
int 0x10
mov bx, the_secret
add bx, 0x7c00
mov al, [bx]
int 0x10

; attempt 4
; 바이트 수를 계산하여 직접 메모리 주소를 지정하는 것이기 때문에 'org' 모드가 적용되지
; 않습니다. 하지만, 바이트 수를 계산하여 직접 메모리 주소를 지정해야 하기 때문에 불편합니다.
mov al, "4"
int 0x10
mov al, [0x7c2d]
int 0x10


jmp $ ; 무한 루프

the_secret:
    ; ASCII 코드 0x58('X')는 Zero-Padding 직전에 저장됩니다.
    ; 이 코드는 0x2D에 위치합니다. ('xxd file.bin'를 사용하여 확인 가능합니다)
    db "X"

; Zero-Padding과 Magic number
times 510-($-$$) db 0
dw 0xaa55
```

위의 코드를 보면, 부트 섹터로 데이터를 출력할 수 있는 표준적인 방법(attempt 2)을 볼 수 있습니다.
변경 사항에 대한 자세한 설명은 위 코드의 주석을 읽으면 이해가 될 것입니다.
