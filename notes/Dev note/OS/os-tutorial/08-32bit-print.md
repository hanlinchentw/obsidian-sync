## What is Protected Mode?
保護模式（Protected Mode）是 Intel CPU 在 80286 之後加入的一種「進階模式」，讓電腦可以做到：
1. **使用更多記憶體（超過 1MB）**
2. **讓程式之間互不干擾（保護記憶體）**
3. **支援多工處理（可以同時跑很多程式）**    
4. **使用 32 位元的指令和暫存器（更快更強）**
---
## 🔁 為什麼叫做「保護」模式？
在早期的「Real Mode」下，任何程式都可以亂寫記憶體，萬一某個程式寫到作業系統的區域，就會導致整台電腦當機。
🛡️ 而「保護模式」的重點就是 —— **保護記憶體**：
- 一個程式不能亂碰別人的記憶體。
- 使用者的程式不能直接操作硬體，避免系統被破壞。
## 為什麼不用 Real Mode 就好？

有**兩個大問題**：
### ❌ 1. 記憶體限制只能用 1MB

這是因為實模式只有 20 位元的記憶體位址（segment:offset 計算出來的）。
→ 對於後來的程式來說，1MB 太小了！
用 **16 位元的暫存器**（AX、BX...）來組成 **20 位元的記憶體地址**，這種計算方式就是：
`address = segment * 16 + offset`
這讓程式很難寫，容易出錯，也限制住了記憶體空間。
🛠️ 所以 Intel 就設計了 **保護模式**，直接支援平坦的 **32 位元地址空間**（0 ～ 4GB），不用再 segment segment 的算。
### ❌ 2. 沒有保護機制
實模式下，所有程式都是「Admin」，想幹嘛就幹嘛。會讓系統不穩定。

## What is VGA?

VGA 就是 **電腦顯示畫面**的早期標準，幫助我們把文字或圖片顯示在螢幕上。
- 最早可以顯示 640x480 的畫面
- 可以切換成「文字模式」（80x25 格）或「圖形模式」
- **BIOS 預設支援的就是 VGA 模式**
### 🛠️ 在開機程式（bootloader）常見這樣寫：
```
mov ah, 0x0e    ; BIOS 功能：印出字元
mov al, 'X'     ; 要印出的字
int 0x10        ; 叫 BIOS 幫你印在螢幕上
```
這就是用 **VGA 文字模式** 在螢幕上顯示 `'X'`。

## What is Video Memory?
VRAM
**Video memory** is a special part of your computer's memory that is used **just for displaying things on the screen** — like text or images.

Think of it like this:
> 🖼️ Whatever is shown on your monitor is stored in video memory.  
> You **write data to video memory**, and it **shows up on the screen**.

**Goal: Print on the screen when on 32-bit protected mode**

The VGA memory starts at address `0xb8000` and it has a text mode which is useful to avoid manipulating direct pixels.

The formula for accessing a specific character on the 80x25 grid is:
`0xb8000 + 2 * (row * 80 + col)`

That is, every character uses `2 bytes` (one for the ASCII, another for color and such), and we see that the structure of the memory concatenates rows.