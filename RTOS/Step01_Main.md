# Purpose

1. 어셈블리어(Assembly), 링커스크립트(Linker Script)가 RTOS 프로젝트에서 하는 역할 파악
2. Register, Exception, ELF의 의미 파악   
3. Makefile의 역할 파악   
<br/>
<br/>

# Step 1. 리셋 벡터
### 리셋 벡터란?

- arm 아키텍쳐 보드 전원 인가 후 처음 읽어들이는 부분(vector)
- 보통 메모리의 맨 처음 주소 (ex: 0x00000000)
- "맨 처음 주소를 읽어들인다" == "리셋벡터에 있는 명령을 실행"   

> **/boot/Entry.S**  
```assembly
.text
	.code 32
	.global vector_start
	.global vector_end

	vector_start:
		MOV R0, R1 

	vector_end:
		.space 1024, 1
.end
```
 
> .text : .end가 나올 때까지 모든 코드가 text 섹션, 보통 code가 들어감  
.code A: 명령어 크기 A비트(ex: code 32 -> 명령어 크기 32비트)   
.global: 외부 파일에서 읽을 수 있게 해줌(c의 extern 역할) -> vector_start, vector_end 외부파일에서 읽을 수 있음   
MOV A, B: B값을 A값에 넣음. (A=B;)   
.space A, B: 현 위치부터 A바이트를 B값으로 채움(ex: space 1024, 1 -> 1024바이트를 1로 채움)
<br/> 
   
**위 code에서 .space 1024, 0을 .space 1024, 1로 바꾸어 실행을 때 실제 메모리에서는?**
```assembly
메모리 주소
0000 0000      MOV R0, R1에 해당하는 기계어 (코드 한줄마다 32비트<4바이트> 차지함)
0000 0004      00000000
...
0000 0400      00000000
```
-> 0000 0004 (=0x00000004<16진수>) 부터 0000 04000(=0x00000400) 까지 0으로 채워져 있어야 함
<br/> 
<br/> 

# Step 2. 링커스크립트
### Qemu란? 
- Guest운영체제가 가상화를 통해 하드웨어 자원에 대한 접근을 할 수 있게 도와주는 프로그램
### ELF 파일이란?
- 전원인가 후 자동으로 실행되는 파일 형식(Executable and Linkable Format)
- 오브젝트 파일들을 하나로 묶은 최종 바이너리 파일
- elf는 header와 section으로 나뉘어 있음
- header: 매직넘버, abi의 버전 및 종류, elf 포맷 버전, 심벌 테이블 오프셋 등.. 정보들
- section: 각 메모리 위치 정보 포함, 여러 정보들을 저장(ex: .bss: 초기화되지 않은 전역변수 정보)

### 링커스크립트란?
- elf형식의 파일들을 하나로 합칠 때 링커가 동작할 방법을 알려주는 파일
- 확장자: .ld

```c
ENTRY(vector_start)
SECTIONS
{
	. = 0x0;

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
> ENTRY(A): 시작 위치의 심벌을 지정.(ex: ENTRY(vector_start) -> vector_start를 시작위치로)   
> SECTIONS{}: 해당 블록이 섹션 배치 설정 정보를 가지고 있음.
> .-0x0: 첫 번째 섹션이 메모리 주소 0x00000000에 위치   
> *(vector_start):.text 섹션의 맨 처음에 저장되므로 0x00000000에 위치
<br/> 
<br/> 

# Step 3. Compile & Link

```bash
$ arm-none-eabi-as -march=armv7-a -mcpu=cortex-a8 -o Entry.o ./Entry.S
$ arm-none-eabi-objcopy -O binary Entry.o Entry.bin
$ hexdump Entry.bin
```
> Entry.S 파일을 컴파일

```bash
$ arm-none-eabi-ld -n -T ./ios.ld -nostdlib -o ios.axf boot/Entry.o
$ arm-none-eabi-objdump -D ios.axf
```
> Entry.o(컴파일한 파일)을 링커로 실행파일 생성
<br/> 
<br/> 

# Step 4. Makefile 
### Makefile이란?
![makefile](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9d76c0ba-d660-4bd2-837d-36a41184f26a%2FUntitled.png?table=block&id=ddc49eb8-0994-4711-a26f-adac5c3ec2d0&width=1640&userId=972842fb-bd14-40a2-b1b1-ca8537a54909&cache=v2)
- 컴파일과 링킹 과정 등 빌드과정의 자동화를 도와주는 파일
- ex) arm-none-eabi-as로 컴파일하고 arm-none-eabi-ld로 링킹하는 과정을 makefile을 통해서 빌드 자동화

> **/Makefile**
```makefile
ARCH = armv7-a
MCPU = cortex-a8

CC = arm-none-eabi-gcc
AS = arm-none-eabi-as
LD = arm-none-eabi-ld
OC = arm-none-eabi-objcopy

LINKER_SCRIPT = ./ios.ld

ASM_SRCS = $(wildcard boot/*.S)
ASM_OBJS = $(patsubst boot/%.S, build/%.o, $(ASM_SRCS))

ios = build/ios.axf
ios_bin = build/ios.bin

.PHONY: all clean run debug gdb kill

all: $(ios)

clean:
	@rm -rf build

run: $(ios)
	qemu-system-arm -M realview-pb-a8 -kernel $(ios)

debug: $(ios)
	qemu-system-arm -M realview-pb-a8 -kernel $(ios) -S -gdb tcp::1234,ipv4

gdb:
	gdb-multiarch

kill:
	kill -9 `ps aux | grep 'qemu' | awk 'NR==1{print $$2}'`

$(ios) : $(ASM_OBJS) $(LINKER_SCRIPT)
	$(LD) -n -T $(LINKER_SCRIPT) -o $(ios) $(ASM_OBJS)
	$(OC) -O binary $(ios) $(ios_bin)

build/%.o: boot/%.S
	mkdir -p $(shell dirname $@)
	$(AS) -march=$(ARCH) -mcpu=$(MCPU) -g -o $@ $<
```


```bash
# 빌드
make all
# QEMU 실행
make run
# QEMU 디버깅 실행
make debug
# 빌드 파일 삭제
make clean
# QEMU 강제 종료
make kill
```
> 명령어로 빌드와 실행을 간단하게 할 수 있음
<br/> 
<br/> 

# Step 5. 메모리 구조 설계   
![memory_architecture](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F09c1fc51-1f48-4afa-bc80-bbe309ae7a94%2FUntitled.png?table=block&id=37de3734-bcdd-445e-87c3-d91052cf5ea6&width=1060&userId=972842fb-bd14-40a2-b1b1-ca8537a54909&cache=v2)
> - Text 영역(1MB) : 0x00000000 ~ 0x000FFFFF
> - USR, SYS(2MB) : 0x00100000 ~ 0x002FFFFF
> - SVC(1MB) : 0x00300000 ~ 0x003FFFFF
> - IRQ(1MB) : 0x00400000 ~ 0x004FFFFF
> - FIQ(1MB) : 0x00500000 ~ 0x005FFFFF
> - ABT(1MB) : 0x00600000 ~ 0x006FFFFF
> - UND(1MB) : 0x00700000 ~ 0x007FFFFF
> - 태스크 스택 영역(64MB) : 0x00800000 ~ 0x047FFFFF
> - 전역 변수 영역(1MB) : 0x04800000 ~ 0x048FFFFF
> - 동적 할당 영역(55MB) : 0x04900000 ~ 0x07FFFFFF
<br/>
<br/>

# Step 6. Exception Vector Table
### Exception 이란?
- 주변장치 등에서 발생한 신호를 처리하기 위해 순차적으로 진행하던 프로그램의 수행을 끊는 것   

### Exception Vector Table 이란?
![Exception_Vector_Table](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F37986669-a842-4e18-a038-70f60861bde6%2FUntitled.png?table=block&id=60802bbc-bc84-4962-8a68-90cabda633ca&width=2820&userId=972842fb-bd14-40a2-b1b1-ca8537a54909&cache=v2)
- ARM에서는 7개의 exception mode가 존재
- Exception이 발생할 때 상황에 맞는 Exception Mode로 벡터 주소 변경 -> 그 부분이 익셉션 벡터 테이블
- Exception Vector Table에 접근 -> exception을 처리하는 코드인 Exception Handler로 점프
<br/>

> **/boot/Entry.S**
```c
#include "ARMv7AR.h"
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
		LDR R0, =0x10000084
		LDR R1, [R0]

	dummy_handler:
		B .
.end
```
> Excption Vector Table의 초기 코드
<br/>

> **/include/MemoryMap.h**
```c
#define INST_ADDR_START     0
#define USRSYS_STACK_START  0x00100000
#define SVC_STACK_START     0x00300000
#define IRQ_STACK_START     0x00400000
#define FIQ_STACK_START     0x00500000
#define ABT_STACK_START     0x00600000
#define UND_STACK_START     0x00700000
#define TASK_STACK_START    0x00800000
#define GLOBAL_ADDR_START   0x04800000
#define DALLOC_ADDR_START   0x04900000

#define INST_MEM_SIZE       (USRSYS_STACK_START - INST_ADDR_START)
#define USRSYS_STACK_SIZE   (SVC_STACK_START - USRSYS_STACK_START)
#define SVC_STACK_SIZE      (IRQ_STACK_START - SVC_STACK_START)
#define IRQ_STACK_SIZE      (FIQ_STACK_START - IRQ_STACK_START)
#define FIQ_STACK_SIZE      (ABT_STACK_START - FIQ_STACK_START)
#define ABT_STACK_SIZE      (UND_STACK_START - ABT_STACK_START)
#define UND_STACK_SIZE      (TASK_STACK_START - UND_STACK_START)
#define TASK_STACK_SIZE     (GLOBAL_ADDR_START - TASK_STACK_START)

#define USR_TASK_STACK_SIZE 0x100000
#define DALLOC_MEM_SIZE     (55 * 1024 * 1024)

#define USRSYS_STACK_TOP    (USRSYS_STACK_START + USRSYS_STACK_SIZE - 4)
#define SVC_STACK_TOP       (SVC_STACK_START + SVC_STACK_SIZE - 4)
#define IRQ_STACK_TOP       (IRQ_STACK_START + IRQ_STACK_SIZE - 4)
#define FIQ_STACK_TOP       (FIQ_STACK_START + FIQ_STACK_SIZE - 4)
#define ABT_STACK_TOP       (ABT_STACK_START + ABT_STACK_SIZE - 4)
#define UND_STACK_TOP       (UND_STACK_START + UND_STACK_SIZE - 4)
```
> 메모리 구조 설계
<br/>

> **/include/ARMv7AR.h**
```c
/* PSR Mode Bit Values */
#define ARM_MODE_BIT_USR 0x10
#define ARM_MODE_BIT_FIQ 0x11
#define ARM_MODE_BIT_IRQ 0x12
#define ARM_MODE_BIT_SVC 0x13
#define ARM_MODE_BIT_ABT 0x17
#define ARM_MODE_BIT_UND 0x1B
#define ARM_MODE_BIT_SYS 0x1F
#define ARM_MODE_BIT_MON 0x16
```
> ARM 동작모드
<br/>

>**/boot/Entry.S** 에 추가
 ```c
reset_handler:
        MRS r0, cpsr
        BIC r1, r0, #0x1F
        ORR r1, r1, #ARM_MODE_BIT_SVC
        MSR cpsr, r1
        LDR sp, =SVC_STACK_TOP

        MRS r0, cpsr
        BIC r1, r0, #0x1F
        ORR r1, r1, #ARM_MODE_BIT_IRQ
        MSR cpsr, r1
        LDR sp, =IRQ_STACK_TOP

        MRS r0, cpsr
        BIC r1, r0, #0x1F
        ORR r1, r1, #ARM_MODEm_BIT_FIQ
        MSR cpsr, r1
        LDR sp, =FIQ_STACK_TOP

        MRS r0, cpsr
        BIC r1, r0, #0x1F
        ORR r1, r1, #ARM_MODE_BIT_ABT
        MSR cpsr, r1
        LDR sp, =ABT_STACK_TOP

        MRS r0, cpsr
        BIC r1, r0, #0x1F
        ORR r1, r1, #ARM_MODE_BIT_UND
        MSR cpsr, r1
        LDR sp, =UND_STACK_TOP

        MRS r0, cpsr
        BIC r1, r0, #0x1F
        ORR r1, r1, #ARM_MODE_BIT_SYS
        MSR cpsr, r1
        LDR sp, =USRSYS_STACK_TOP
```
> 위 코드는 다음과 같은 내용의 반복
> ```c
> MRS r0, cpsr
> BIC r1, r0, #0x1F
> ORR r1, r1, #동작 모드
> MSR cpsr, r1
> LDR sp, =스택 꼭대기 메모리 주소
> ```
> MRS A, B: 
> BIC A, B, C:
> ORR A, B, C:
> MSR A, B:
> LDR A, B:
<br/>
<br/>

# Step 7. Main
> **Entry.S**
> ```c
> reset_handler:
>          
>        ...
> 
>        BL  main
> ```
> reset_handler에 BL main 명령어 추가 -> c언어 코드로 진입
<br/>

> **boot/Main.c**
> "stdint.h": `https://gist.github.com/SKrns/111d0278936f5948a7e72920f5396083`
> ```c
> #include "stdint.h"
> 
> void main(void)
> {
> 	uint32_t* dummyAddr = (uint32_t*)(1024*1024*100);
> 	*dummyAddr = sizeof(long);
> }
> ```
> c언어 코드
<br/>

> **/.Makefile**
```bash
ARCH = armv7-a
MCPU = cortex-a8

CC = arm-none-eabi-gcc
AS = arm-none-eabi-as
LD = arm-none-eabi-ld
OC = arm-none-eabi-objcopy

LINKER_SCRIPT = ./ios.ld
MAP_FILE = build/ios.map

ASM_SRCS = $(wildcard boot/*.S)
ASM_OBJS = $(patsubst boot/%.S, build/%.os, $(ASM_SRCS))

C_SRCS = $(wildcard boot/*.c)
C_OBJS = $(patsubst boot/%.c, build/%.o, $(C_SRCS))

INC_DIRS = -I include

ios = build/ios.axf
ios_bin = build/ios.bin

.PHONY: all clean run debug gdb kill

all: $(ios)

clean:
	@rm -rf build

run: $(ios)
	qemu-system-arm -M realview-pb-a8 -kernel $(ios)

debug: $(ios)
	qemu-system-arm -M realview-pb-a8 -kernel $(ios) -S -gdb tcp::1234,ipv4

gdb:
	gdb-multiarch

kill:
	kill -9 `ps aux | grep 'qemu' | awk 'NR==1{print $$2}'`

$(ios) : $(ASM_OBJS) $(C_OBJS) $(LINKER_SCRIPT)
	$(LD) -n -T $(LINKER_SCRIPT) -o $(ios) $(ASM_OBJS) $(C_OBJS) -Map=$(MAP_FILE)
	$(OC) -O binary $(ios) $(ios_bin)

build/%.os: $(ASM_SRCS)
	mkdir -p $(shell dirname $@)
	$(CC) -march=$(ARCH) -mcpu=$(MCPU) $(INC_DIRS) -c -g -o $@ $<

build/%.o: $(C_SRCS)
	mkdir -p $(shell dirname $@)
	$(CC) -march=$(ARCH) -mcpu=$(MCPU) $(INC_DIRS) -c -g -o $@ $<
```
> 최종 수정 makefile

```bash
$ make all
$ make debug
...
$ gdb-multiarch
...
(gdb) target remote:1234
...
(gdb) file build/ios.axf
...
(gdb) c
...
^C
(gdb) x/8wx 0x6400000
```
> gdb에서 `info register` 명령어로 현재 레지스터 정보를 읽을 수 있고, `s` 명령어로 코드 한줄 씩 실행도 가능합니다. 마지막의  `x/8wx 0x6400000` 명령어로 main함수의 실행 결과(100MB의 위치에 4 저장)를 확인 할 수 있습니다.
