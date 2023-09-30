# 14 лабораторная

Вывести в разных строчках экрана ФИО, факультет и группу при загрузке системы.
```
org 7C00h

start:
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

 mov ax, 1301h
 mov bp, message1
 mov cx, 23
 mov bl, 02h
 int 10h

 mov dh, 1
 mov dl, 0
 xor bh, bh

 mov ax, 1301h
 mov bp, message2
 mov cx, 3
 mov bl, 02h
 int 10h

 mov dh, 2
 mov dl, 0
 xor bh, bh

 mov ax, 1301h
 mov bp, message3
 mov cx, 3
 mov bl, 02h
 int 10h

 jmp $

message1 db 'Konstantin Konstantinov',0
message2 db '403',0
message3 db 'FMF',0
times 510 - ($ - $$) db 0
db 0x55, 0xAA
```
![изображение](https://github.com/tarioma/ASM/assets/125894838/e513423f-dd34-4781-bf33-45357d010b64)
