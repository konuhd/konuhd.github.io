---
layout: post
title: "[HECTF2024]easyreee" 
date:  2024-12-11 11:28:12 +0530
categories: jekyll update
---
# [HECTF2024]easyreee
### 用16进制打开exe文件后，将全部"CTF"字样替换为"UPX"
### 再用upx -d 进行脱壳
+ # 进入main

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int data[27]; // [rsp+20h] [rbp-A0h]
  int k; // [rsp+8Ch] [rbp-34h]
  char str[30]; // [rsp+90h] [rbp-30h] BYREF
  int flag; // [rsp+B8h] [rbp-8h]
  int i; // [rsp+BCh] [rbp-4h]

  _main();
  data[0] = 35;
  data[1] = 33;
  data[2] = 32;
  data[3] = 40;
  data[4] = 37;
  data[5] = 126;
  data[6] = 40;
  data[7] = 70;
  data[8] = 82;
  data[9] = 4;
  data[10] = 75;
  data[11] = 82;
  data[12] = 76;
  data[13] = 3;
  data[14] = 82;
  data[15] = 4;
  data[16] = 72;
  data[17] = 79;
  data[18] = 123;
  data[19] = 79;
  data[20] = 125;
  data[21] = 66;
  data[22] = 68;
  data[23] = 4;
  data[24] = 79;
  data[25] = 73;
  data[26] = 112;
  flag = 0;
  std::operator>><char,std::char_traits<char>>(refptr__ZSt3cin, str);
  encrypt(str, 8);
  if ( strlen(str) != 27 )
  {
    flag = 1;
    printf("Your length is wrong 0.0");
  }
  puts("Guess how many xor's there are");
  std::istream::operator>>(refptr__ZSt3cin);
  for ( i = 0; i <= 26; ++i )
  {
    if ( str[i] - 21 != (data[i] ^ k) )
    {
      flag = 1;
      break;
    }
  }
  if ( flag == 1 )
    puts("YOU ARE WRONG!");
  else
    puts("Genius!");
  return 0;
}
```

由flag第6位是"{",求出k.
再写个逆向函数

```python
import string
k=0
data = [
    35, 33, 32, 40, 37, 126, 40, 70, 82, 4,
    75, 82, 76, 3, 82, 4, 72, 79, 123, 79,
    125, 66, 68, 4, 79, 73, 112
]
while True:
    if ord("{")-21==data[5]^k:
        break
    k+=1
s=bytearray(27)
for i in range(27):
    s[i]=(data[i]^k)+21
print(s)
cot=8
for i in range(len(s)):
    if chr(s[i]) in string.ascii_lowercase:
        s[i] = (s[i] - 97 -cot) % 26 + 97
        cot += 1
    elif chr(s[i]) in string.ascii_uppercase:
        s[i]=(s[i] - 65 -cot) % 26 + 65
        cot+=1
print(s)
```
## flag:bytearray(b'HECTF{Re_1s_s0_1nterest1ng}')
## 即HECTF{Re_1s_s0_1nterest1ng}
