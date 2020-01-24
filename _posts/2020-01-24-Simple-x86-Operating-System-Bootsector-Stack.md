---
layout  : post
title   : "[Simple x86 Operating System] Bootsector Stack"
date    : 2020-01-24 13:18:04 +0900
category: Simple-x86-Operating-System
---

이 글에서는 스택(Stack) 사용 방법을 알아볼것입니다.

- `SP` (Stack Pointer register) : 스택의 최상단을 가리키는 포인터로 사용됩니다.
- `BP` (Stack Base Pointer register) : 스택의 베이스를 가리키는 포인터로 사용됩니다.

`BP` 레지스터는 스택의 기본 주소(즉, 맨 아래)를 저장하고, `SP`는 맨 위를 저장하고 스택은 `BP`에서 아래쪽으로 커지게 됩니다. (즉, `SP`는 감소됨)

`boot_sect_stack.asm`의 코드를 보도록 하겠습니다.

```nasm
mov ah, 0x0e ; tty mode

mov bp, 0x8000 ; 이것은 0x7C00(Loaded Boot Sector)에서 멀리 떨어진 주소이므로, 덮어쓰지 않습니다.
mov sp, bp ; 스택이 비어있으면 sp는 bp를 가리킵니다.

push 'A'
push 'B'
push 'C'

; 스택이 아래쪽으로 어떻게 나아가는지 보여주기 위해
mov al, [0x7ffe] ; 0x8000 - 2
int 0x10

; [0x8000]에 접근하려 하지 마세요, 지금 작동하지 않습니다.
; 스택의 상단에만 접근할 수 있으므로, 이 시점에서는 0x7FFE 접근이 가능합니다.
mov al, [0x8000]
int 0x10


; 'pop'을 사용하여 문자를 다시 가져옵니다.
; 전체를 pop 하므로 하위 바이트를 조작하려면 보조 레지스터가 필요합니다.
pop bx
mov al, bl
int 0x10 ; prints C

pop bx
mov al, bl
int 0x10 ; prints B

pop bx
mov al, bl
int 0x10 ; prints A

; Stack에서 pop된 데이터는 이제 쓰레기 값입니다.
mov al, [0x8000]
int 0x10


jmp $
times 510-($-$$) db 0
dw 0xaa55
```

![boot_sect_stack.bin QEMU]({{ site.url }}/assets/image/2020-01-24-Simple-x86-Operating-System-Bootsector-Stack/2020-01-24-Simple-x86-Operating-System-Bootsector-Stack_1.png)
