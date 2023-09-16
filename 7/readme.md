# 7 лабораторная
### 1 вариант

В соответствии с вариантом напишите программу на языке C++ и на языке ассемблера (с помощью ассемблерных вставок). Сравните полученный результат.

![изображение](https://github.com/tarioma/ASM/assets/125894838/91e6f624-faff-44de-975b-1c035a998f44)

#
### №1
C++
```
int main() {
    int a = 6;
    int x = 5;
    int y1, y2, y;

    if (x > a) {
        y1 = a + x;
    }
    else {
        y1 = 2*a - x;
    }

    if (x > 10) {
        y2 = a * x;
    }
    else {
        y2 = x;
    }

    y = y1 + y2;
}
```
10М прогонов по 5 раз (среднее): 6 321 мкс
# 
Assembler
```
int a = 6;
int x = 5;
int y1, y2, y;

__asm {
    mov eax, a
    mov ebx, x
    cmp ebx, eax
    jg x_greater_a
    jmp x_less_or_equal_a

    x_greater_a:
        mov ecx, a
        add ecx, ebx  // a + x
        mov y1, ecx
        jmp define_y2

    x_less_or_equal_a:
        mov ecx, a
        imul ecx, 2  // 2 * a
        sub ecx, ebx  // 2a - x
        mov y1, ecx

    define_y2:
        cmp ebx, 10
        jg x_greater_10
        jmp x_less_or_equal_10

        x_greater_10:
            imul eax, ebx  // a * x
            mov y2, eax
            jmp solve

        x_less_or_equal_10:
            mov y2, ebx

    solve:
        mov eax, y1
        add eax, y2
        mov y, eax
}
```
10М прогонов по 5 раз (среднее): 8 263 мкс

