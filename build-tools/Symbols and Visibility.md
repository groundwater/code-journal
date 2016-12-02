# Symbols and Visibility

- See my [learn linkers project](https://github.com/groundwater/learn-linkers)

## Links

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
