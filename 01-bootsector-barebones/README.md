*Concepts you may want to Google beforehand: assembler, BIOS*

**Goal: Create a file which the BIOS interprets as a bootable disk**

This is very exciting, we're going to create our own boot sector!

Theory
------

When the computer boots, the BIOS doesn't know how to load the OS, so it
delegates that task to the boot sector. Thus, the boot sector must be
placed in a known, standard location. That location is the first sector
of the disk (cylinder 0, head 0, sector 0) and it takes 512 bytes.

To make sure that the "disk is bootable", the BIOS checks that bytes
511 and 512 of the alleged boot sector are bytes `0xAA55`.

This is the simplest boot sector ever:

```
e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[ 29 more lines with sixteen zero-bytes each ]
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
```

It is basically all zeros, ending with the 16-bit value
`0xAA55` (beware of endianness, x86 is little-endian). 
The first three bytes perform an infinite jump

Simplest boot sector ever
-------------------------

You can either write the above 512 bytes
with a binary editor, or just write a very
simple assembler code:

```nasm
; Infinite loop (e9 fd ff)
loop:                   ; Define a label , " loop ", that will allow
                        ; us to jump back to it , forever.
        
    jmp loop            ; Use a simple CPU instruction that jumps
                        ; to a new memory address to continue execution.
                        ; In our case , jump to the address of the current
                        ; instruction.

times 510-($-$$) db 0  ;When compiled , our program must fit into 512 bytes ,
                       ; with the last two bytes being the magic number ,
                       ; so here , tell our assembly compiler to pad out our
                       ; program with enough zero bytes (db 0) to bring us to the
                       ; 510 th byte.

dw 0xaa55              ; Last two bytes ( one word ) form the magic number ,
                       ; so BIOS knows we are a boot sector.

```

To compile:
`nasm -f bin boot_sect_simple.asm -o boot_sect_simple.bin`

> OSX warning: if this drops an error, read chapter 00 again

I know you're anxious to try it out (I am!), so let's do it:

`qemu boot_sect_simple.bin`

> On some systems, you may have to run `qemu-system-x86_64 boot_sect_simple.bin` If this gives an SDL error, try passing the --nographic and/or --curses flag(s).

> On windows you need to add the `.exe` after selecting the qemu type. For eg. for windows, you may have to run `qemu-system-x86_64.exe boot_sect_simple.bin` from the directory where qemu is stored.      

You will see a window open which says **" Booting from Hard Disk... "** and
nothing else. When was the last time you were so excited to see an infinite
loop? ;-)
