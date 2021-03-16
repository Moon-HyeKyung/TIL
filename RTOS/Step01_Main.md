Purpose
어셈블리어(Assembly), 링커스크립트(Linker Script)가 RTOS 프로젝트에서 하는 역할 파악
Register, Exception, ELF의 의미 파악
Makefile의 역할 파악
Step 1. 리셋 벡터
리셋 벡터란?
arm 아키텍쳐 보드 전원 인가 후 처음 읽어들이는 부분(vector)
보통 메모리의 맨 처음 주소 (ex: 0x00000000)
"맨 처음 주소를 읽어들인다" == "리셋벡터에 있는 명령을 실행"
.text
	.code 32
	.global vector_start
	.global vector_end

	vector_start:
		MOV R0, R1 

	vector_end:
		.space 1024, 1
.end
/boot/Entry.S
.text : .end가 나올 때까지 모든 코드가 text 섹션, 보통 code가 들어감
.code A: 명령어 크기 A비트(ex: code 32 -> 명령어 크기 32비트)
.global: 외부 파일에서 읽을 수 있게 해줌(c의 extern 역할) -> vector_start, vector_end 외부파일에서 읽을 수 있음
MOV A, B: B값을 A값에 넣음. (A=B;)
.space A, B: 현 위치부터 A바이트를 B값으로 채움(ex: space 1024, 1 -> 1024바이트를 1로 채움)

위 code에서 .space 1024, 0을 .space 1024, 1로 바꾸어 실행을 때 실제 메모리에서는?

메모리 주소
0000 0000      MOV R0, R1에 해당하는 기계어 (코드 한줄마다 32비트<4바이트> 차지함)
0000 0004      00000000
...
0000 0400      00000000
-> 0000 0004 (=0x00000004<16진수>) 부터 0000 04000(=0x00000400) 까지 0으로 채워져 있어야 함

Step 2. 링커스크립트
Qemu란?
Guest운영체제가 가상화를 통해 하드웨어 자원에 대한 접근을 할 수 있게 도와주는 프로그램
ELF 파일이란?
ㅇㄹㅇㄴ
링커스크립트란?
ㅇㄴㄹㅇㄴ
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
ㅇㄹㅇㄴㄴ

Step 3. MakeFile
Step 4. 메모리 구조
Step 5. Exception Table
Step 6. Main

