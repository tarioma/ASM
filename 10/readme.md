# 9 лабораторная
### 1 вариант

Написать программу, которая находит сумму значений функции _y = 2x<sup>2</sup> + 5x - 3_ в диапазоне от 1 до 10 с шагом 1.

#
C++
```
int main() {
    int a[10] = { 12, 53, -5, 10, 11, -7, 20, 32, -12, 15 };
    int b[10], c[10];

    for (int i = 0; i < 10; i++) {
        b[i] = a[i] / -3;
        c[i] = abs(a[i] % -3);
    }

    for (int i = 0; i < 10; i++) {
        cout << b[i] << ' ';
    }

    cout << endl;

    for (int i = 0; i < 10; i++) {
        cout << c[i] << ' ';
    }
}
```
10М прогонов по 5 раз (среднее): 22 271 мкс
# 
Assembler
```
int main() {
    int a[10] = { 12, 53, -5, 10, 11, -7, 20, 32, -12, 15 };
    int b[10], c[10];
    
    __asm {
        mov edi, 0
        mov ecx, 10
        mov esi, -3  // Делитель
        m:
            mov eax, [a + edi * 4]
            test eax, eax
            js negative  // Делимое отрицательное
            xor edx, edx
            idiv esi
            mov[b + edi * 4], eax
            jmp remaind
    
        negative:
            neg eax
            xor edx, edx
            idiv esi
            neg eax
            mov[b + edi * 4], eax
    
        remaind:
            mov[c + edi * 4], edx  // Остаток от деления
    
        inc edi
        loop m
    }
    
    for (int i = 0; i < 10; i++) {
        cout << b[i] << ' ';
    }
    
    cout << endl;
    
    for (int i = 0; i < 10; i++) {
        cout << c[i] << ' ';
    }
}
```
10М прогонов по 5 раз (среднее): 19 592 мкс
