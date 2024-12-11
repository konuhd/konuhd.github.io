# [HECTF]littleasm

+ ## 题目
题目如下
```asm
   .section   .data
flag: .space 28
key:  .ascii "rev"
data: .byte 0x6a,0x28,0x3d,0x4e,0x2b,0x5,0x63,0x1e,0xd,0x73,0x10,0x1c,0x73,0x24,0x21,0x73,0x5e,0x21,0x31,0x5d,0x21,0x3f,0xc,0xd,0x6d,0x4c,0x3
msg_wrong:  .ascii "WRONG!!!\n"
msg_right:  .ascii "Right!!\n"
   .section   .text
   .globl  main
main:
    pushl   %ebp
    movl    %esp, %ebp
    subl    $28, %esp

    leal    flag, %eax
    pushl   %eax
    call    scanf
    addl    $4, %esp

    movl    $0, %ecx
encrypt_loop:
    cmpl    $27, %ecx
    jge     check_loop

    movl    %ecx, %edx
    addl    $2, %edx
    movl    $3, %eax
    divl    %eax
    movzx   %dl, %edx
    movzx   data(,%ecx,1), %eax
    movzx   flag(,%ecx,1), %ebx
    movzx   key(,%edx,1), %esi
    xorl    %ebx, %esi
    addl    $0x2C, %esi
    movb    %sil, data(,%ecx,1)

    movl    %ecx, %edx
    addl    $1, %edx
    movl    $3, %eax
    divl    %eax
    movzx   %dl, %edx
    movzx   data(,%ecx + 1,1), %eax
    movzx   flag(,%ecx + 1,1), %ebx
    movzx   key(,%edx,1), %esi
    xorl    %ebx, %esi
    addl    $0x8, %esi
    movb    %sil, data(,%ecx + 1,1)

    movl    %ecx, %edx
    movl    $3, %eax
    divl    %eax
    movzx   %dl, %edx
    movzx   data(,%ecx + 2,1), %eax
    movzx   flag(,%ecx + 2,1), %ebx
    movzx   key(,%edx,1), %esi
    xorl    %ebx, %esi
    xorl    $0xC, %esi
    movb    %sil, data(,%ecx + 2,1)

    addl    $3, %ecx
    jmp     encrypt_loop

check_loop:
    movl    $0, %ecx
    movl    $0, %edx
compare_loop:
    cmpl    $27, %ecx
    jge     check_result

    movzx   flag(,%ecx,1), %eax
    movzx   data(,%ecx,1), %ebx
    cmpb    %al, %bl
    jne     set_wrong

    addl    $1, %ecx
    jmp     compare_loop

set_wrong:
    movl    $1, %edx

check_result:
    cmpl    $0, %edx
    jne     print_wrong

    leal    msg_right, %eax
    pushl   %eax
    call    printf
    addl    $4, %esp

    jmp     end_main

print_wrong:
    leal    msg_wrong, %eax
    pushl   %eax
    call    printf
    addl    $4, %esp

end_main:
    movl    %ebp, %esp
    popl    %ebp
    ret
```

观察到encrypt_loop为加密函数
书写解密函数
```python
data=[0x6a,0x28,0x3d,0x4e,0x2b,0x5,0x63,0x1e,0xd,0x73,0x10,
      0x1c,0x73,0x24,0x21,0x73,0x5e,0x21,0x31,0x5d,0x21,0x3f,
      0xc,0xd,0x6d,0x4c,0x3]
key="rev"
flag=bytearray(27)
for  i in range(0,9):

    key_index_1=(3*i+2)%3
    flag[3*i]=(data[3*i]-0x2C)^ord(key[key_index_1])

    key_index_2=(3*i+1)%3
    flag[3*i+1]=(data[3*i+1]-0x8)^ord(key[key_index_2])

    key_index_3=(3*i)%3
    flag[3*i+2]=data[3*i+2]^0xC^ord(key[key_index_3])
print(flag)
```

得出flag:bytearray(b'HECTF{Ass1mb1y_13_s0_eas7!}')