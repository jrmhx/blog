---
title: "Multidimensional Arrays and Pointers in C"
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

Multi-dimensional arrays in C can be somewhat confusing, especially when it comes to pointers and memory layout. This blog post aims to give beginners in C a kick-off and disenchant these concepts. In my humble opinion, if you master the pointers, you master the core concept of C language.

### Before Starting:
- This post is inspiried by [this question on stackoverflow](https://stackoverflow.com/questions/77730288/passing-double-pointers-as-2d-arrays-in-c/).
- For an easy kick-off, better if you already have these pre-knowledges (but no worries if you don't, I'll share some links):
  - basic understanding of memory address & pointers in C:
    - https://www.w3schools.com/c/c_memory_address.php
    - https://www.w3schools.com/c/c_pointers.php
  - an array (whatever how many dimensions it is) in C as long as it's a **static allocated** array is stored **contiguously** in memory
  - an online IDE with compiler that might come in handy: [jdoodle C](https://www.jdoodle.com/c-online-compiler/)
  

### What's the Issue?

Let's understand the problem first, here's a C code snippet **with bugs**.  
Don't worry what does the `convert_to_csr` do. You just need to focus on its arguments.
```c
#include<stdio.h>

void convert_to_csr(int **matrix, size_t row_len, size_t col_len){
    printf("first value of array %d\n", **matrix);
}

int main(void) {
    int matrix[3][3] = {
        {0, 1, 0},
        {0, 0, 1},
        {0, 1, 0}
    };
    printf("matrix first value outside func %d\n", matrix[0][0]);
    
    convert_to_csr(matrix, 3, 3);
} 
```

Simply copy and paste it to the [jdoodle online C compiler](https://www.jdoodle.com/c-online-compiler/) and run it. A error will pop-up like `Command terminated by signal 11`  which aka `SIGSEGV`. The error appears when a program or code tries to access an invalid memory location.

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

So The function `convert_to_csr` is expecting a parameter of type `int**`, which is a pointer to a pointer to an `int`. This is fundamentally different from `int (*)[3]` in terms of memory layout and how the pointer arithmetic works. That's why the error has been thrown.

### What's the Solutions?

A generic solution in C for passing 2D or multi-dimensional array as paramenter is using `void*` pointer. 

But please be careful because `void*` pointers lack any information about the type they point to, like the pointered-to variable's size. So when working with `void*` pointers, you have to explicitly manage the pointer arithmetic and type casting.

An example using `void*` in this case is:
```c
#include<stdio.h>

void convert_to_csr(void *matrix, size_t row_len, size_t col_len){
    // Cast the void pointer to the correct type, which is a pointer to an array of int[col_len]
    int (*mat)[col_len] = matrix;

    // Now, mat can be used as a 2D array
    // Some 2D array accessing operations
    for (size_t i = 0; i < row_len; ++i) {
        for (size_t j = 0; j < col_len; ++j) {
            printf("%d ", mat[i][j]);
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

    printf("Matrix first value outside func: %d\n", matrix[0][0]);

    // Passing the matrix as a void pointer
    convert_to_csr(matrix, 3, 3);

    return 0;
}
```
This `void*` approach could be useful if your compiler doesn't support variable length array arguments.

---

Or this will work as well, but it uses some magic number in `convert_to_csr` declaration so I don't really like it.
```c
#include <stdio.h>
#include <stdlib.h>

void convert_to_csr(int (*matrix)[3], size_t row_len, size_t col_len) {
    // Example: Print elements to demonstrate correct access
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
    printf("Matrix first value outside func: %d\n", matrix[0][0]);
    
    convert_to_csr(matrix, 3, 3);

    return 0;
} 

```

---

Or if your compiler supports variable length arrays then you can declare the `convert_to_csr` function like:
```c
#include <stdio.h>

void convert_to_csr(size_t row_len, size_t col_len, int matrix[row_len][col_len]){
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
    
    convert_to_csr(3, 3, matrix);
} 
```
This is a cleaner and more straightforward way of handling arrays of varying sizes in C, provided that the compiler supports C99 or later standards.

### Last Talking Cock:
If you understand the pointers and mutidimentional arrays in C, you already A MASTER OF C! 💃🏻💃🏻💃🏻🕺🏻🕺🏻🕺🏻

Lol but honestly, in C there's not necessary many cool stuffs (like `vector` in C++ or GC in Java). That why you might find C usually under the shadow of C++🥲.

Even though, I reckon that understanding that Memory Allocation stuff is critical for learning whatever kinds of programming languages. **It's not just the language but the mindset as a programmer**. 

For an advanced understanding of C, computer architecture, compilation principle and even a bit linux system kernel, please have a look at [CMU 15-213: CSAPP](https://csapp.cs.cmu.edu/). It's not easy, but no cap that it's my first inspired course in computer science study jounery. It indeed cooked most of the fundamentals you need to know in computer science. Its reputation is well deserived🙂.

### Some References:
- [StackOverflow: passing double pointers as 2d arrays in C](https://stackoverflow.com/questions/77730288/passing-double-pointers-as-2d-arrays-in-c/)
- [GeeksforGeeks: lvalue and rvalue in C language](https://www.geeksforgeeks.org/lvalue-and-rvalue-in-c-language/)
- [CppReference: C99 variable length arrays](https://en.cppreference.com/w/c/language/array#Variable-length_arrays)
- [CMU 15-213: CSAPP](https://csapp.cs.cmu.edu/)

