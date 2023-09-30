# 15 лабораторная

3. Используя конструкцию Call /Ret выполните задание при выводе с использованием функции.
```
org 7C00h
cli
xor ax, ax
mov ds, ax
mov es, ax
mov ss, ax
mov sp, 07C00h
sti
mov ax, 3
int 10h

mov ah, 2h
mov dh, 0
mov dl, 0
xor bh, bh
int 10h

call print_hello  ; Вызов подпрограммы print_hello

jmp $  ; Бесконечный цикл

print_hello:  ; Подпрограмма print_hello
    mov ax, 1301h
    mov bp, message
    mov cx, 12
    mov bl, 02h
    int 10h
    ret

message db 'Hello World!', 0

times 510 - ($ - $$) db 0
dw 0xAA55
```
![изображение](https://github.com/tarioma/ASM/assets/125894838/b47db49d-d69f-4127-a600-42b9612794e0)

#
4. На основе программы, выполненной в 3, выполните задание 2.
```
org 7C00h
cli
xor ax, ax
mov ds, ax
mov es, ax
mov ss, ax
mov sp, 07C00h
sti
mov ax, 3
int 10h

mov ah, 2h
mov dh, 0
mov dl, 0
xor bh, bh
int 10h

mov dh, 0
call print_fcs

mov dh, 1
call print_faculty

mov dh, 2
call print_group_number

jmp $

print_fcs:
    mov ax, 1301h
    mov bp, message1
    mov cx, 23
    mov bl, 02h
    int 10h
    ret

print_faculty:
    mov ax, 1301h
    mov bp, message2
    mov cx, 3
    mov bl, 02h
    int 10h
    ret

print_group_number:
    mov ax, 1301h
    mov bp, message3
    mov cx, 3
    mov bl, 02h
    int 10h
    ret

message1 db 'Konstantin Konstantinov', 0
message2 db 'FMF', 0
message3 db '403', 0

times 510 - ($ - $$) db 0
dw 0xAA55
```
![изображение](https://github.com/tarioma/ASM/assets/125894838/0fb30954-d61a-4063-9698-0b0cbc10b393)
