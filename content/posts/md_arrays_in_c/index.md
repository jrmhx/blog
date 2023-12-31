---
title: "2D Arrays and Pointers in C"
date: 2023-12-29T18:10:00+11:00
# weight: 1
# aliases: ["/first"]
tags: ["C", "array", "pointer", "memory", "segmentation fault"]
author: "Jeremiah Hsing"
# author: ["Me", "You"] # multiple authors
cover:
  image: "<image path/url>" # image path/url
  alt: "<alt text>" # alt text
  caption: "<text>" # display caption under cover
  relative: false # when using page bundles set this to true
  hidden: true # only hide on current single page
categories: ["Programming", "C"]
draft: false
---

### Before Starting:

- basic understanding of memory address & pointers in C:
  - https://www.w3schools.com/c/c_memory_address.php
  - https://www.w3schools.com/c/c_pointers.php
- [2D arrays in C](/posts/2d_array_c)
- an online IDE with compiler that might come in handy: [jdoodle C](https://www.jdoodle.com/c-online-compiler/)

### What's the Issue?

Let's understand the problem first, here's a C code snippet **with bugs**.  
```c
#include<stdio.h>

void traverse(int **matrix, size_t row_len, size_t col_len){
  for (int i = 0; i < row_len; i++){
    for (int j = 0; j < col_len; j++){
      printf("%d ", matrix[i][j]);
    }
    printf("\n");
  }
}
int main(void) {
  int matrix[3][3] = {
    {0, 1, 0},
    {0, 0, 1},
    {0, 1, 0}
  };
  
  traverse(matrix, 3, 3);
} 
```

Simply copy and paste and run it. Most likely you will see `Command terminated by signal 11`. 

### Why SIGSEGV Happens?

Now let's break down these different kinds of "pointers" in C to understand the problem:

---
- A Normal Pointer (like `int*`): 
  - `int* ptr`: A pointer in C is a variable that holds the memory address of another variable.
  - Size of a pointer jump: `ptr++` in `int *ptr` moves the pointer by `sizeof(int)` bytes.
- Array Name:
  - `int arr[5]`: in this case `arr` is not exactly a 100% pointer. As this "pointer" does not allow the increment operation (++). This is because arrays are not modifiable [l-values](https://www.geeksforgeeks.org/lvalue-and-rvalue-in-c-language/) in C.
  - However, you can use a new pointer `int* ptr = arr` as in C, the name of an array can be used as a pointer to its first element. 
  - Size of a pointer jump: `ptr++` in this case moves the pointer by `sizeof(int)` bytes; 
- And if it's a 2D array `int arr[5][5]`:
  - Then the first element is an `int[5]` array, so a pointerizion in this case should be: `int (*ptr)[5] = arr`. 
  - Size of a pointer jump: `ptr++` in this case moves the pointer by `5 * sizeof(int)` bytes.
- A Pointer to Pointer: 
  - `int ** ptr`: A pointer to pointer in C is a variable that holds the memory address of another pointer.
  - Size of a pointer jump: `ptr++` in `int **ptr` moves the pointer by `sizeof(int*)` bytes aka a poniter's size.

---

So The function `traverse` is expecting a parameter of type `int**`, (a pointer to a pointer to an `int`). This is different from `int (*)[3]` in terms of memory layout and how the pointer arithmetic works. 

Please run the following code:

```c
#include<stdio.h>

int main(void) {
    int matrix[3][3] = {
        {0, 1, 0},
        {0, 0, 1},
        {0, 1, 0}
    };
   
    int **ptr1 = matrix;
    int (*ptr2)[3] = matrix;
    printf("ptr1: %p\n", ptr1);
    printf("ptr2: %p\n", ptr2);
    printf("*ptr1: %p\n", *ptr1);
    printf("*ptr2: %p\n", *ptr2);
    return 0;
} 
```

You can get the output like:
```
ptr1: 0x7ffe5261a090
ptr2: 0x7ffe5261a090
*ptr1: 0x100000000
*ptr2: 0x7ffe5261a090
```
Your `ptr1`, `ptr2`,and `*ptr2` might be different than mine (as we run on different machine) but the third line `*ptr1: 0x100000000` should be the same and it doesn't looks like an actual address at all.

When you dereference `ptr1` using `*ptr1`, you're treating the value at `matrix[0][0]` as a pointer (since `ptr1` is an `int**`). This is why you get a strange and seemingly unrelated memory address `0x100000000`. It's interpreting the integer value at `matrix[0][0]` as a memory address.

Please notice that operation like `int **ptr1 = matrix;` is actually problematic and should not compile without a **warning** or **error** in standard C. These are not compatible types. If your compiler allows this assignment, it's doing so without proper type checking, that is unsafe.

### What's the Solutions?

If your compiler supports variable length arrays (VLA) then you can declare the `traverse` function like:
```c
#include <stdio.h>

void traverse(size_t row_len, size_t col_len, int matrix[row_len][col_len]){
    for (size_t i = 0; i < row_len; i++) {
        for (size_t j = 0; j < col_len; j++) {
            printf("%d ", matrix[i][j]);
        }
        printf("\n");
    }
}

int main(void) {
    int matrix[3][3] = {
        {0, 1, 0},
        {0, 0, 1},
        {0, 1, 0}
    };
    printf("matrix first value outside func %d\n", matrix[0][0]);
    
    traverse(3, 3, matrix);
} 
```
This is a cleaner and more straightforward way of handling arrays of varying sizes in C, provided that the compiler supports C99 or later standards.

