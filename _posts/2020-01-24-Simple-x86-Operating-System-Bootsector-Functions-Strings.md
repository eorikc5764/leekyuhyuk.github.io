---
layout  : post
title   : "[Simple x86 Operating System] Bootsector Functions Strings"
date    : 2020-01-24 19:59:10 +0900
category: Simple-x86-Operating-System
---

이 글에서는 어셈블러를 사용하여 기본 항목(루프, 함수)를 코딩하는 방법을 알아보겠습니다.

# 문자열

바이트와 같은 문자열을 정의하되 끝을 결정할 수 있도록 Null 바이트로 종료합니다.

```nasm
mystring:
    db 'Hello, World', 0
```

따옴표로 묶인 텍스트는 어셈블러에서 ASCII로 변환되는 반면, `0`은 바이트 `0x00`(Null 바이트)로 전달됩니다.

# 제어문

우리는 이미 무한 루프에 `jmp $`를 사용했습니다.

어셈블러의 점프(Jump)는 이전 명령 결과에 의해 정의됩니다. 예를 들면 다음과 같습니다.

```nasm
cmp ax, 4      ; ax가 4이면,
je ax_is_four  ; 무엇인가를 합니다. (ax_is_four 라벨로 점프합니다.)
jmp else       ; 4가 아니라면, 다른 무엇인가를 합니다.
jmp endif      ; 정상적인 흐름을 재개합니다.

ax_is_four:
    .....
    jmp endif

else:
    .....
    jmp endif  ; 실제로 필요하진 않지만 완성도를 위해 넣었습니다.

endif:
```

**Unsigned 계열 (부호가 없는 값) :**

| 약어        | 뜻                           | 용도                                  |
|-------------|------------------------------|---------------------------------------|
| `je`        | Jump Equal                   | 비교 결과가 같을 때 점프              |
| `jne`       | Jump Not Equal               | 비교 결과가 다를 때 점프              |
| `jz`        | Jump Zero                    | 결과가 0일 때 점프                    |
| `jnz`       | Jump Not Zero                | 결과가 0이 아닐 때 점프               |
| `ja`        | Jump Above                   | `cmp a,b`에서 `a`가 클 때 점프        |
| `jae`       | Jump Above or Equal          | 크거나 같을 때 점프                   |
| `jna`       | Jump Not Above               | 크지 않을 때 점프                     |
| `jnae`      | Jump Not Above or Equal      | 크지 않거나 같지 않을 때 점프         |
| `jb`        | Jump Below                   | `cmp a,b`에서 `a`가 작을 때 점프      |
| `jbe`       | Jump Below or Equal          | 작거나 같을 때 점프                   |
| `jnb`       | Jump Not Below               | 작지 않을 때 점프                     |
| `jnbe`      | Jump Not Below or Equal      | 작지 않거나 같지 않을 때 점프         |
| `jc`        | Jump Carry                   | Carry Flag가 1일 때 점프              |
| `jnc`       | Jump Not Carry               | Carry Flag가 0일 때 점프              |
| `jnp`/`jpo` | Jump Not Parity / Parity Odd | Parity Flag가 0일 때 / 홀수일 때 점프 |
| `jp`/`jpe`  | Jump Parity / Parity Even    | Parity Flag가 1일 때 / 짝수일 때 점프 |
| `jecxz`     | Jump ECX Zero                | ECX 레지스터가 0일때 점프             |

**Signed 계열 (부호가 있는 값) :**

| 약어       | 뜻                           | 용도                                               |
|------------|------------------------------|----------------------------------------------------|
| `jg`       | Jump Greater                 | `cmp a,b`에서 `a`가 클 때 점프                     |
| `jge`      | Jump Greater or Equal        | 크거나 같을 때 점프                                |
| `jng`      | Jump Not Greater             | 크지 않을 때 점프                                  |
| `jnge`     | Jump Not Greater or Equal    | 크지 않거나 같지 않을 때 점프                      |
| `jl`       | Jump Less                    | `cmp a,b`에서 `a`가 작을 때 점프                   |
| `jle`      | Jump Less or Equal           | 작거나 같을 때 점프                                |
| `jnl`      | Jump Not Less                | 작지 않을 때 점프                                  |
| `jnle`     | Jump Not Less or Equal       | 작지 않거나 같지 않을 때 점프                      |
| `jo`/`jno` | Jump Overflow / Not Overflow | Overflow Flag가 1일 때 / 0일 때 점프               |
| `js`/`jns` | Jump Sign / Not Sign         | Sign(부호) Flag가 1일 때(음수) / 0일 때(양수) 점프 |

# 함수 호출

함수 호출은 Label로 이동하는 것입니다. 여기서 까다로운 부분은 매개변수(Parameter)입니다. 매개 변수 작업에는 두 단계가 있습니다.

1. 프로그래머는 특정 레지스터 또는 메모리 주소를 공유한다는 것을 알고 있습니다.
2. 조금 더 많은 코드를 작성하고, 함수 호출을 부작용(Side Effect) 없이 만듭니다.

1단계는 쉽습니다. 매개 변수에 `al`(실제로는 `ax`)를 사용합니다.

```nasm
mov al, 'X'
jmp print
endprint:

...

print:
    mov ah, 0x0e  ; tty code
    int 0x10      ; 이미 'al'에 문자를 가지고 있다고 가정합니다.
    jmp endprint  ; 이 Label은 사전에 작성되어 있다고 가정합니다.
```

이 접근 방식은 코드가 스파게티 코드로 간다는 것을 알 수 있습니다. 현재 `print`기능은 `endprint`로만 돌아갑니다. 코드 재사용을 안 하고 있음을 볼 수 있습니다.

이러한 문제점을 피하기 위해서 2가지 개선 사항이 있습니다.

- 반환 주소(Return Address)이 저장될 수 있도록 합니다.
- 레지스터가 부작용이 없이 수정될 수 있도록 현재 레지스터를 저장합니다.

반환 주소를 저장하기 위해 CPU가 도움이 될 것입니다. 서브루틴(Subroutine)을 호출하기 위해 몇 개의 `jmp`를 사용하는 대신 `call`과 `ret`을 사용하세요.

레지스터 데이터를 저장하기 위해 스택을 사용하는 특수 명령이 있습니다.  
`pusha`와 `popa`는 모든 레지스터를 자동으로 스택에 넣고 나중에 복구합니다.

# 외부 파일 포함

어셈블러에서는 외부 파일을 포함할 수 있습니다.  
문법은 다음과 같습니다.

```nasm
%include "file.asm"
```

# 16진수 값 출력

올바른 데이터를 읽고 있는지 확인하는 방법이 필요합니다. 아래의 `boot_sect_print_hex.asm` 파일은 `boot_sect_print.asm`을 확장하여 ASCII 문자뿐만 아니라 16진수 바이트를 출력합니다.

코드를 보면, `boot_sect_print.asm`와 `boot_sect_print_hex.asm`은 기본 파일에 `%include`되는 서브루틴입니다. 루프를 사용하여 화면에 바이트를 출력합니다. 또한 개행을 출력하는 기능도 포함합니다. `\n`은 실제로 2바이트이며 개행 문자는 `0x0A`, 캐리지 리턴(Carriage Return)은 `0x0D`입니다. 캐리지 리턴을 제거하여 실험해보고 그 효과도 확인해보세요.

main 파일인 `boot_sect_main.asm`은 몇 개의 문자열과 바이트를 로드하고, `print`와 `print_hex`를 호출한 후 정지됩니다.

`boot_sect_main.asm`:
```nasm
[org 0x7c00] ; 오프셋이 부트 섹터 코드임을 어셈블러에게 알립니다.

; 주요 루틴은 매개 변수가 준비되었는지 확인한 다음 함수를 호출합니다.
mov bx, HELLO
call print

call print_nl

mov bx, GOODBYE
call print

call print_nl

mov dx, 0x12fe
call print_hex

; 무한 루프
jmp $

; 무한 루프 아래에 서브 루틴을 포함해야합니다.
%include "boot_sect_print.asm"
%include "boot_sect_print_hex.asm"


; 데이터
HELLO:
    db 'Hello, World', 0

GOODBYE:
    db 'Goodbye', 0

; 이전 코드의 크기에서 510까지 0으로 채웁니다.
times 510-($-$$) db 0

; Magic number
dw 0xaa55
```

`boot_sect_print.asm`:
```nasm
print:
    pusha

; 아래를 명심하세요:
; while (string[i] != 0) { print string[i]; i++ }

; 문자열 끝(Null 바이트) 비교
start:
    mov al, [bx] ; 'bx'는 문자열의 기본 주소(Base Address) 입니다.
    cmp al, 0 ; 문자열의 끝(Null)인지 확인합니다.
    je done

    ; BIOS의 도움으로 출력하는 부분
    mov ah, 0x0e
    int 0x10 ; 'al'은 이미 문자를 포함하고 있습니다.

    ; 증분 포인터(Increment Pointer) 및 다음 루프 실행
    add bx, 1
    jmp start

done:
    popa
    ret

print_nl:
    pusha

    mov ah, 0x0e
    mov al, 0x0a ; 개행 문자
    int 0x10
    mov al, 0x0d ; Carriage Return (현재 위치를 나타내는 커서를 맨 앞으로 이동)
    int 0x10

    popa
    ret
```

`boot_sect_print_hex.asm`:
```nasm
; 'dx'로 데이터를 받습니다.
; 예제에서는 dx=0x1234로 호출했다고 가정합니다.
print_hex:
    pusha

    mov cx, 0 ; our index variable

; 'dx'의 마지막 문자를 가져온 다음 ASCII로 변환하는 방법을 사용할 것입니다.
; 숫자 ASCII 값 : '0'(ASCII 0x30) ~ '9'(ASCII 0x39)이므로, 바이트 N에 0x30을 추가합니다.
; 알파벳 문자 A-F: 'A' (ASCII 0x41) ~ 'F' (0x46)이므로, 0x40을 추가합니다.
; 그런 다음, ASCII 바이트를 문자열의 올바른 위치로 이동시킵니다.
hex_loop:
    cmp cx, 4 ; 4회 반복
    je end

    ; 1. 'dx'의 마지막 문자를 ASCII로 변환
    mov ax, dx ; 'ax'를 작업 레지스터로 사용합니다.
    and ax, 0x000f ; 앞의 3개의 0을 마스킹 하여 0x1234를 0x0004로 만듭니다.
    add al, 0x30 ; ACSII "N"으로 변환하려면 0x30을 N에 더합니다.
    cmp al, 0x39 ; 9보다 큰 경우, 8을 더하여 'A'에서 'F'를 나타냅니다.
    jle step2
    add al, 7 ; 'A'는 58 대신 ASCII 65이므로, 65 - 58 = 7

step2:
    ; 2. ASCII 문자를 배치할 문자열의 올바른 위치를 가져옵니다.
    ; bx <- base address + string length - index of char
    mov bx, HEX_OUT + 5 ; base + length
    sub bx, cx  ; index variable
    mov [bx], al ; 'al'의 ASCII 문자를 'bx'가 가리키는 위치로 복사
    ror dx, 4 ; 0x1234 -> 0x4123 -> 0x3412 -> 0x2341 -> 0x1234

    ; index를 증가시키면서 반복합니다.
    add cx, 1
    jmp hex_loop

end:
    ; 매개변수를 준비하고 함수를 호출합니다.
    ; print는 'bx'로 매개 변수를 받는다는 것을 기억해야 합니다.
    mov bx, HEX_OUT
    call print

    popa
    ret

HEX_OUT:
    db '0x0000',0 ; 새 문자열을 위한 메모리 예약
```

![boot_sect_main.bin QEMU]({{ site.url }}/assets/image/2020-01-24-Simple-x86-Operating-System-Bootsector-Functions-Strings/2020-01-24-Simple-x86-Operating-System-Bootsector-Functions-Strings_1.png)

**캐리지 리턴을 제거하여 실행했을 때 :**  
![boot_sect_main.bin QEMU]({{ site.url }}/assets/image/2020-01-24-Simple-x86-Operating-System-Bootsector-Functions-Strings/2020-01-24-Simple-x86-Operating-System-Bootsector-Functions-Strings_2.png)
