# Symbols and Visibility

- See my [learn linkers project](https://github.com/groundwater/learn-linkers)

## Toolset

- `elfdump` on linux
- `otool` on osx
- `objdump` (or `gobjdump` installed with homebrew)
- `lldb`
  - `(lldb) target create --no-dependents XXYY.so`
  - `(lldb) image dump symtab`

## Mach-O

Mach-O is a file format structured as follows

- headers
- segments
  - sections (zero or more)

- Examine sections
  
  ```
  bash-4.4$ size -x -l -m main
  Segment __PAGEZERO: 0x100000000 (vmaddr 0x0 fileoff 0)
  Segment __TEXT: 0x1000 (vmaddr 0x100000000 fileoff 0)
          Section __text: 0x81 (addr 0x100000f30 offset 3888)
          Section __unwind_info: 0x48 (addr 0x100000fb4 offset 4020)
          total 0xc9
  Segment __LINKEDIT: 0x1000 (vmaddr 0x100001000 fileoff 4096)
  total 0x100002000
  ```
## Log

- It looks like when compiling an object, you indicate the type of visibility you want with `-fvisibility=XX`.
  
  - Dump an individual object build with `-fvisibility=hidden` with `gobjdump`
  
    ```
    bash-4.4$ gobjdump -x build/1.o

    build/1.o:     file format mach-o-x86-64
    build/1.o
    architecture: i386:x86-64, flags 0x00000011:
    HAS_RELOC, HAS_SYMS
    start address 0x0000000000000000

    Sections:
    Idx Name          Size      VMA               LMA               File off  Algn
      0 .text         00000031  0000000000000000  0000000000000000  000001d0  2**4
                      CONTENTS, ALLOC, LOAD, CODE
      1 __LD.__compact_unwind 00000040  0000000000000038  0000000000000038  00000208  2**3
                      CONTENTS, RELOC, DEBUGGING
      2 .eh_frame     00000068  0000000000000078  0000000000000078  00000248  2**3
                      CONTENTS, ALLOC, LOAD, READONLY, DATA
    SYMBOL TABLE:
    0000000000000000 g       1f SECT   01 0000 [.text] __Z6addOnei
    0000000000000020 g       1f SECT   01 0000 [.text] __Z6addTwoi


    RELOCATION RECORDS FOR [__LD.__compact_unwind]:
    OFFSET           TYPE              VALUE
    0000000000000020 64                .text
    0000000000000000 64                .text
    ```
  - Dump a shared library build with objects using `-fvisibility=hidden`
  
    ```
    bash-4.4$ gobjdump -x libadd.so

    libadd.so:     file format mach-o-x86-64
    libadd.so
    architecture: i386:x86-64, flags 0x00000050:
    HAS_SYMS, DYNAMIC
    start address 0x0000000000000000

    Sections:
    Idx Name          Size      VMA               LMA               File off  Algn
      0 .text         0000006d  0000000000000f40  0000000000000f40  00000f40  2**4
                      CONTENTS, ALLOC, LOAD, CODE
      1 __TEXT.__unwind_info 00000048  0000000000000fb0  0000000000000fb0  00000fb0  2**2
                      CONTENTS, ALLOC, LOAD, READONLY, CODE
    SYMBOL TABLE:
    0000000000000f40 g       1e SECT   01 0000 [.text] __Z6addOnei
    0000000000000f60 g       1e SECT   01 0000 [.text] __Z6addTwoi
    0000000000000f80 g       1e SECT   01 0000 [.text] __Z8timesTwoi
    0000000000000fa0 g       1e SECT   01 0000 [.text] __Z10timesThreei
    0000000000000000 g       01 UND    00 0200 dyld_stub_binder
    ```
- Dump with `objdump` on hidden symbols
  
  ```
  bash-4.4$ objdump -t libadd.so

  libadd.so:      file format Mach-O 64-bit x86-64

  SYMBOL TABLE:
  0000000000000f40 l     F __TEXT,__text  __Z6addOnei
  0000000000000f60 l     F __TEXT,__text  __Z6addTwoi
  0000000000000f80 l     F __TEXT,__text  __Z8timesTwoi
  0000000000000fa0 l     F __TEXT,__text  __Z10timesThreei
  0000000000000000         *UND*  dyld_stub_binder
  ```
- Same lib dumped with `nm -g` (for global/exported symbols).
  
  ```
  bash-4.4$ nm -g libadd.so
                 U dyld_stub_binder
  ```
  
  Without `-g` we see all the expected symbols
  
  ```
  bash-4.4$ nm libadd.so
  0000000000000fa0 t __Z10timesThreei
  0000000000000f40 t __Z6addOnei
  0000000000000f60 t __Z6addTwoi
  0000000000000f80 t __Z8timesTwoi
                   U dyld_stub_binder
  ```

## Links

- [How to Read an Executable](https://jvns.ca/blog/2014/09/06/how-to-read-an-executable/)
- [How do UNIX linkers work](http://webpages.charter.net/ppluzhnikov/linker.html)
- https://linux.die.net/man/1/ld
- http://stackoverflow.com/questions/6687630/how-to-remove-unused-c-c-symbols-with-gcc-and-ld
- https://gcc.gnu.org/wiki/Visibility
- https://gcc.gnu.org/ml/gcc-help/2003-08/msg00128.html
  
  ```
  -ffunction-sections
  Keeps funcitons in separate data sections, so they can be discarded if unused.
  
  -Wl,--gc-sections
  Tell the linker to garbage collect and discard unused sections.
  ```
