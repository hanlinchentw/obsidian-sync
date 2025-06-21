
**Goal: Learn how the computer memory is organized**

當我們嘗試印出 the_secret `X` 時，
```assembly
mov ah, 0x0e

mov al, the_secret
int 0x10

the_secret:
    db "X"
    
times 510-($-$$) db 0
dw 0xaa55
```
我們會得到這樣的結果，顯然不是我們預期的 X
![[截圖 2025-06-20 晚上7.06.42.png]]

因為實際上在跑 `boot sector` 的時候，你的 `code` 其實沒有放在 `0x00`，因此其實真正資料存放的地點與你認為的地點有 `offset` 存在。
BIOS places it at `0x7C00`

![[Pasted image 20250620184206.png]]

既然如此，那我們就先 offset 0x7c00 來看看我們找不找得到 the_secret
首先我們把 the_secret move 到 bx register
接著把 bx offset 0x7c00
最後將 bx 的內容 move 到
```
mov ah, 0x0e

mov bx, the_secret
add bx, 0x7c00
mov al, [bx]
int 0x10

jmp $ ; infinite loop

the_secret:
    db "X"

; zero padding and magic bios number
times 510-($-$$) db 0
dw 0xaa55
```

也可以在文件開頭加上 [org 0x7c00]

```assembly
[org 0x7c00]
mov ah, 0x0e

mov al, [the_secret]
int 0x10

jmp $

the_secret:
    db "X"
times 510-($-$$) db 0
dw 0xaa55

```