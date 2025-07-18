**Goal: Set up the Interrupt Descriptor Table to handle CPU interrupts**

### **Things you should know**

#### 1. `#include` guard 

`#include` guard 是 C/C++ 裡常用來避免 **重複包含（重複定義）**的技巧。  
用最簡單的例子來說明：
假設你有一個 `myheader.h`：

```
// myheader.h
int add(int a, int b);
```

然後你在兩個 `.c` 檔案裡都 `#include "myheader.h"`，但這兩個 `.c` 又都被另外一個檔案 `main.cpp` 引用時，這個 `myheader.h` 就會被包含兩次 ➜ 可能導致 **重複定義錯誤**。
### Usage

```c
// myheader.h
#ifndef MYHEADER_H    // 如果還沒定義 MYHEADER_H
#define MYHEADER_H    // 那就定義它

int add(int a, int b);

#endif  // 結尾
```


#### 2. Type attributes
`packed`, `extern`, `volatile`, and `exceptions`

1. packed
Compiler 有時會為了讓資料存取更快，自動對齊（alignment）資料。舉個例子：
比如一個 `MyStruct`
```c
struct MyStruct {
	char a; // 1 byte
	int b; // 4 bytes
}
```
在沒有 `packed` 標注的情況下，compiler 會在 `a` 後面自動加上 **3 bytes 的 padding**，讓 `b` 的起始位址是 4 的倍數（符合 `int` 的對齊需求）。

```less
// 記憶體分布：
| a |   |   |   | b | b | b | b |
  0   1   2   3   4   5   6   7
```
這樣可以讓 CPU 更有效率地讀取資料，因為大多數 CPU 在存取對齊的記憶體時速度比較快，甚至在某些平台（像 ARM）如果資料**沒有對齊，還會造成 crash**。
如果沒有這些 padding，像 `b` 這樣的 `int` 有可能會被排在地址 1，造成 CPU 需要做額外操作，甚至無法正確存取。

那麼為什麼還要用 `packed` 呢？
在某些特殊情境下，比如：
- **Embedded system**
- **網路封包結構（protocol packet）**
- **二進位資料格式**
memory 很珍貴、或格式需要精準一致，這時候你可以使用 `__attribute__((packed))` 或 `#pragma pack` 來**關閉 padding**，節省空間，並保持與外部格式一致。

2. extern
告訴 Compiler 這個變數或是函數有被定義在某個地方，不用擔心。
```c
// file1.c
int counter = 10;

// file2.c
extern int counter;  // tells the compiler "it's declared elsewhere"
```

Without `extern`, if you declare `int counter;` again in file2, you’d get a **duplicate symbol error** at link time.

3. volatile

「這個變數**隨時可能會被其他東西改變**，就算程式碼中看起來沒改變，也**不要做優化**。」

`volatile int flag`

**Why:** 
Compiler 在優化程式時，會**假設變數不會改變**，除非它看到你自己寫了修改的程式碼。
但在某些情況下（尤其是底層系統開發），像是：
- 硬體暫存器（Hardware registers）
- 中斷處理（Interrupts）
- 多執行緒共享變數（Multithreaded shared flags）
變數可能會在你**看不到的地方被改變**。

`volatile` **不是用來保證執行緒安全的**，它只保證每次都從記憶體真正讀取或寫入，而不是用暫存變數。


### IDT (Interrup descriptor table)


IDT 是一個特定於 **IA-32 和 x86-64 架構** 的二進位資料結構。

當電腦系統進入 **保護模式（Protected Mode）或長模式（Long Mode）** 時，CPU 不再使用傳統的中斷向量表（IVT），而是透過 IDT 來處理中斷。

這張表告訴 CPU 當發生中斷時，應該執行哪一段「中斷服務程式（ISR, Interrupt Service Routine）」。每一個中斷向量對應到 IDT 裡的一個項目。

IDT 的結構跟「全域描述表（GDT）」有點類似，不過它是專門用來處理中斷的。

在 IDT 中可以有三種 descriptor：**task-gate descriptor**、**interrupt-gate descriptor**、和 **trap-gate descriptor**

<span style="color:red">Before you implement the IDT, make sure you have a working GDT.</span> 
****
Structure in IA-32
```c
struct InterruptDescriptor32 {
   uint16_t offset_1;        // offset bits 0..15
   uint16_t selector;        // a code segment selector in GDT or LDT
   uint8_t  zero;            // unused, set to 0
   uint8_t  type_attributes; // gate type, dpl, and p fields
   uint16_t offset_2;        // offset bits 16..31
};
```

Example _type_attributes_ values that people are likely to use (assuming DPL is 0):
- 32-bit **Interrupt Gate: 0x8E** (p=1, dpl=0b00, type=0b1110 => type_attributes=0b1000_1110=**0x8E**)
- 32-bit **Trap Gate: 0x8F** (p=1, dpl=0b00, type=0b1111 => type_attributes=1000_1111b=**0x8F**)
- **Task Gate: 0x85** (p=1, dpl=0b00, type=0b0101 => type_attributes=0b1000_0101=**0x85**)

Structure in x86_64
```c
struct InterruptDescriptor64 {
   uint16_t offset_1;        // offset bits 0..15
   uint16_t selector;        // a code segment selector in GDT or LDT
   uint8_t  ist;             // bits 0..2 holds Interrupt Stack Table offset, rest of bits zero.
   uint8_t  type_attributes; // gate type, dpl, and p fields
   uint16_t offset_2;        // offset bits 16..31
   uint32_t offset_3;        // offset bits 32..63
   uint32_t zero;            // reserved
};
```
Example _type_attributes_ values that people are likely to use (assuming DPL is 0):
- 64-bit **Interrupt Gate: 0x8E** (p=1, dpl=0b00, type=0b1110 => type_attributes=0b1000_1110=**0x8E**)
- 64-bit **Trap Gate: 0x8F** (p=1, dpl=0b00, type=0b1111 => type_attributes=1000_1111b=**0x8F**)