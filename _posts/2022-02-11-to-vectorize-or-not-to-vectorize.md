Exploiting the inherent <u>data parallelism</u> in your problem is essential to obtain a high performance software solution on modern processor architectures. This task is usually left to the compiler; nevertheless it is important to understand the limitations of compiler optimizations and its failure cases. However, in this short article, we will not address these failure cases. Instead, we will explore a specific case in which the compiler goes even further and optimizes away vector operations opting instead for a <u>single operation</u>.

This specific case, as trivial as it is, illustrates a key point: you should not compete with your compiler, instead understand how it behaves, and quantitatively measure your software's performance. If you require more performance, only then look at the compiler's output and figure out why it is spitting out what you see. Anyway, let's get right into it.

Consider the loop inside the function `foo`. It is clear that this loop is <u>vectorizable</u>, since no iteration depends on previous or next iterations. So, it would seem reasonable that the optimal way to implement this loop in assembly is through vector operations (i.e., using the SSE or AVX ISA extensions).

```c
void foo(uint8_t* a, uint8_t* b) {
	for (uint64_t i = 0; i < SIZE; ++i)
    	a[i] = b[i + 1];
}
```

Indeed, the GNU x86-64 GCC compiler vectorizes the loop producing the following x86 assembly. Note the use of vector move instructions and vector registers under the `.L3` label.

```assembly
foo:
        lea     rdx, [rsi+2]
        mov     rax, rdi
        sub     rax, rdx
        cmp     rax, 14
        mov     eax, 0
        jbe     .L2
.L3:
        movdqu  xmm0, XMMWORD PTR [rsi+1+rax]
        movups  XMMWORD PTR [rdi+rax], xmm0
        add     rax, 16
        cmp     rax, 65536
        jne     .L3
        ret
.L2:
        add     rax, 1
        movzx   edx, BYTE PTR [rsi+rax]
        mov     BYTE PTR [rdi-1+rax], dl
        cmp     rax, 65536
        jne     .L2
        ret
```

However, looking at the action of the loop, we can see that it is simply copying memory from a series of consecutive locations to another. So, we should be able to replace the entire loop with a single call to the `memcpy` function. Indeed we can instruct the compiler to do so by giving it an extra piece of information: the `restrict` keyword. By adding this keyword to our function definition as shown below, we are telling the compiler that the memory regions of the two pointers do not overlap. This extra piece of information allows the compiler to use `memcpy` instead of vector operations.

```c
void bar(uint8_t* restrict a, uint8_t* restrict b) {
	for (uint64_t i = 0; i < SIZE; ++i)
    	a[i] = b[i + 1];
}
```

```assembly
bar:
        add     rsi, 1
        mov     edx, 65536
        jmp     memcpy
```

















