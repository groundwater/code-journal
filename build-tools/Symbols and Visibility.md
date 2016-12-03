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
