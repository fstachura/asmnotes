# useful commands

* `objdump -d -m intel obj_file` - disassemble obj_file

# gdb

https://visualgdb.com/gdbreference/commands/

## execution

* ni - next instruction
* si - step into function
* finish - execute until function returns
* cont - execute until breakpoint
* break - add breakpoint/list breakpoints if without args
* clear - remove breakpoint
* delete - remove breakpoint by nr
* backtrace - show call stack
* run < input - cat `input` file to stdin

## layout

* set disassembly-flavor intel - set instruction format to intel
* layout asm - add asm pane
* layout regs - add regs pane
* layout src - add source pane
* tui focus cmd/asm/regs/src - change cursor focus
* tui reg xmm/float/all/general/sse - change register type in regs pane
* refresh - refresh screen

## info

* info reg
* info float
* info all-reg
* info stack
* info functions
* info frame
* maintenance info sections
* info files

## print

#### commands

* `print [/f] [expr]` - print value
* `p /x $eax`
* `x /nf addr` - print value under address
* `x/100x $sp` - print stack

#### format 

* x - hexadecimal
* d - signed decimal
* u - unsigned decimal
* o - octal
* t - binary
* a - address
* c - character
* f - floating point

#### extra modifiers

* n - how many elements
* u - size of a single element (b=1, h=2, w=4, g=8) (if format without size used, like x)
* f - format like in print + for x: s for strings, i for asm

## shortcuts

* c-x o - change window

# nasm 

* `.label` - label local to the closest previous label (with one dot less)
* `global` - declare exported symbols
* `extern` - declare external symbols
* `section .text` - name a section

## macros

* `%define name value` - substitutes name for value
* `%define name(arg) arg*2` - creates a parametrized macro, `arg` argument from macro call will be substituted in macro value
* `%include "file"` - replaced with contents of `file`

## data

* `name size data` 
* size - db, dw, dd, dq...

# abi64

http://www.nasm.us/links/unix64abi
https://gitlab.com/x86-psABIs/x86-64-ABI

* integer or pointer args - rdi, rsi, rdx, rcx, r8, r9
* float args - xmm0...xmm7
* rax - number of float args
* rest of arguments passed on the stack
* rbx, rbp, rsp, r12, r13, r14, r15 have to stay unchanged after function call
* result - rax,rdx for pointers and integers, xmm0/1 for floats

## structs

* very complex
* if the struct is passed by value and is small enough, individual fields will be passed by registers
* pairs of fields may be packed into registers (ex. int and float, both 32bit, into rdi)
* it seems that constructors do not return valid data in rax
* returning by value - pointer to space for the result is passed in rdi by the caller

# g++/itanium-cxx name mangling

https://itanium-cxx-abi.github.io/cxx-abi/abi.html#mangling

* `c++filt` - name demangler
* starts with `_Z` 
* return type is not encoded for non template function names (otherwise return type is the first parameter)
* all function types encode at least one parameter type (void)
* classes, enums or unions are encoded just as the name (see example 2)
* nested names (namespaces) - N, (length, name) pair for each level, E
* template types after the name, start with I and end with E
* references to template types - T_, T0_, T1_...
* repeating argument types are replaced with substitutions (except for scalar builtin types, not pointers)- referenced by S_, S0_...
* operators have special, separate encoding and are not encoded as a function name
* qualifiers - r (restrict), K (const), V (volatile)

## constructors and destructors 

* C1 - complete object constructor
* C2 - base object constructor
* C3 - complete object allocating constructor
* D0 - deleting destructor
* D1 - complete object destructor
* D2 - base object destructor

## selected types

* v - void
* b - bool
* c - char
* h - unsigned char
* s - short
* i - int
* j - unsigned int
* l - long
* m - unsigned long
* f - float
* d - double
* P - pointer
* r - l-value reference
* o - r-value reference
* A <number> _ <type> - array

## selected operators

* pl - `+`
* mi - `-`
* ml - `*`
* dv - `/`
* aS - `=`             
* pL - `+=`            
* mI - `-=`           
* mL - `*=`
* eq - `==`
* ne - `!=`
* lt - `<`             
* gt - `>`             
* le - `<=`            
* ge - `>=`            
* pp - `++`
* mm - `--`
* cl - `()`           
* ix - `[]`
* ls - `<<`
* rs - `>>`

## examples

* `_Z4testPFvvE` - `test(void (*)())`
* `_Z4testdPFidEi5Class` - `test(double, int (*)(double), int, Class)`
* `_Z4testdPfidEi5ClassA4_d` - `test(double, int (*)(double), int, class, double [4])`
* `_Z4testdPFidEi5ClassA4_dN9namespace4testE` - `test(double, int (*)(double), int, class, double [4], namespace::test)`
* `_Z4testIidcET_T1_T0_` - `int test<int, double, char>(char, double)`

# registers

* rax/eax/ax/ah:al
* rbx
* rcx
* rdx
* rsi/esi/si/sil
* rdi/edi/di/dil
* rbp/ebp/bp/bpl
* rsp/esp/sp/spl
* r8-r15, r8d-r15d, r8w-r15w, r8b-r15b

# instructions

* r/m - either register or memory address

## transfer

* xchg r/m r
* xchg r r/m

## arithmetic

* adc - add with carry
* sbb - sub with borrow, also substracts carry flag
* adc/add/sub/sbb r/m r/imm
* adc/add/sub/sbb r r/m
* mul r/m - unsigned multiply, rdx:rax = rax*r/m

## binary operations

* rol - rotate left
* ror - rotate right
* shl - shift left
* shr - shift right
* shl/shr/rol/ror r/m, imm8/cl 

## jumps

* jmp 

#### conditional jumps

* ja - above (CF=0 and ZF=0)
* jae - above or equal (CF=0)
* jb - below (CF=1)
* jbe - below or equal (CF=1 or ZF=1)
* jc - carry (CF=1)
* je - equal (ZF=1)
* jg - greater (ZF=0 and SF=OF)
* jge - greater or equal (SF=OF)
* jl - less (SF<>OF)
* jle - less or equal (ZF=1 or SF<>OF)
* jo - overflow (OF=1)
* jp - parity (PF=1)
* jpe - parity even (PF=1)
* jpo - parity odd (PF=0)
* js - sign (SF=1)
* jz - 0 (ZF=1)
* jna - not above (CF=1 or ZF=1)
* jnae - not above or equal (CF=1)
* jnb - not below (CF=0)
* jnbe - not below or equal (CF=0 and ZF=0)
* jnc - not carry (CF=0)
* jne - not equal (ZF=0)
* jng - not greater (ZF=1 or SF<>OF)
* jnge - not greater or equal (SF<>OF)
* jnl - not less (SF=OF)
* jnle - not less or equal (ZF=0 and SF=OF)
* jno - not overflow (OF=0)
* jnp - not parity (PF=0)
* jns - not sign (SF=0)
* jnz - not zero (ZF=0)

#### cmp

* cmp r/m r/imm
* cmp r r/m
* cmp a, b
* jl => a < b
* above/below for unsigned
* greater/less for signed

## stack

* push r/m
* push imm
* pop r/m

## sse

### basics

* http://www.songho.ca/misc/sse/sse.html
* https://www.officedaytime.com/simd512e/
* s - scalar, p - packed 
* s - single precision, d - double
* ss - single precision scalar, ps - packed scalar
* a - aligned, u - unaligned
* instructions without aligned/unaligned versions seem to segfault on unaligned addresses

### transfer

#### scalar s/d move

* vmovss xmm1 {w}, xmm2, xmm3 - merge from xmm2 and xmm3 to xmm1
* movss xmm1 {w}/m32, xmm2 - move from xmm2 to xmm1 or memory
* movss xmm1 {w}, m32 - move from memory to xmm1 
* same with movsd, but m64
* w - optional writemask TODO
* movlps/movhps xmm1, m64 - move two packed single precision two lower/higher parts

#### packed integer/s/d move

* mov{a,u}ps xmm1, xmm2/m128 - move packed single precision values
* mov{a,u}ps xmm1/m128, xmm2
* movdq{a,u} xmm1, xmm2/m128 - move packed integer values
* movdq{a,u} xmm1/m128, xmm2

#### shuffle

* shufps xmm1, xmm3/m128, imm8
* movhlps xmm1, xmm2 - move high quadword of xmm2 to low quadword of xmm1

### arithmetic

* psubb/psubw/psubd xmm1, xmm2/m128- substract packed integers
* subps/addps/mulps/divps xmm1, xmm2/m128 - sub/add/mul/div packed single precision
* sqrtps xmm1, xmm2/m128 - compute sqrt of xmm2, save in xmm1

# sources 

* https://www.felixcloutier.com/x86/
* https://ww2.ii.uj.edu.pl/~kapela/pn/listlectures.php
* https://itanium-cxx-abi.github.io/cxx-abi/abi.html
* https://en.wikipedia.org/wiki/name_mangling
* https://gitlab.com/x86-psabis/x86-64-abi/
* https://visualgdb.com/gdbreference/commands/

