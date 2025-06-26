
`#include` guard 是 C/C++ 裡常用來避免 **重複包含（重複定義）**的技巧。  
用最簡單的例子來說明：
假設你有一個 `myheader.h`：

```
// myheader.h
int add(int a, int b);
```

然後你在兩個 `.c` 檔案裡都 `#include "myheader.h"`，但這兩個 `.c` 又都被另外一個檔案 `main.cpp` 引用時，這個 `myheader.h` 就會被包含兩次 ➜ 可能導致 **重複定義錯誤**。
### `include` guard 的做法

```c
// myheader.h
#ifndef MYHEADER_H    // 如果還沒定義 MYHEADER_H
#define MYHEADER_H    // 那就定義它

int add(int a, int b);

#endif  // 結尾
```


### Type attributes
`packed`, `extern`, `volatile`, and `exceptions`

