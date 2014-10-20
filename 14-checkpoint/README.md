*Concepts you may want to Google beforehand: monolithic kernel, microkernel, debugger, gdb*

**Goal: Pause and organize our code a little bit**

Maybe you didn't realize it, but you already have your own kernel
running!

However, it does very little, just print an 'X'. Now is the time to stop for
a moment and organize the code into folders, create a scalable Makefile for future code,
and think on a strategy.

Take a look at the new folder structure. Most of the files have been symlinked
from previous lessons, so if we have to change them at some point, it will be
a better idea to remove the symlink and create a new file.

Furthermore, since from now on we will use mostly C to code, we'll take advantage of qemu's
ability to open a connection to gdb. First, let's install a cross-compiled `gdb` since
OSX uses `lldb` which is not compatible with the ELF file format (neither is the `gdb` available
on Homebrew's repos)

```sh
cd /tmp/src
curl -O http://ftp.rediris.es/mirror/GNU/gnu/gdb/gdb-7.8.tar.gz
tar xf gdb-7.8.tar.gz
mkdir gdb-build
cd gdb-build
export PREFIX="/usr/local/i386elfgcc"
export TARGET=i386-elf
../gdb-7.8/configure --target="$TARGET" --prefix="$PREFIX" --program-prefix=i386-elf-
make
make install
```

Check out the Makefile target `make debug`. We can take
advantage of this cool qemu feature. Type `make debug` and, on the gdb shell:

- Set up a breakpoint in `kernel.c:main()`: `b main`
- Run the OS: `continue`
- Run two steps into the code: `next` then `next`. You will see that we are just about to set
  the 'X' on the screen, but it isn't there yet (chech out the qemu screen)
- Let's see what's in the video memory: `print *video_memory`. There is the 'L' from "Landed in
  32-bit Protected Mode"
- Hmmm, let's make sure that `video_memory` points to the correct address: `print video_memory`
- `next` to put there our 'X'
- Let's make sure: `print *video_memory` and look at the qemu screen. It's definitely there.

Now is a good time to read some tutorial on `gdb`!


Strategy
--------

We will want to do many things with our OS:

- Handle screen output and keyboard input, for starters
- A tiny, basic `libc`
- Memory management
- Interrupt handling
- Write a filesystem to store files
- Create a very simple shell
- Maybe we will write a simple text editor
- Multiple processes and scheduling

Probably we will go through them in that order, however it's soon to tell.

If we feel brave enough:

- A BASIC interpreter, like in the 70s!
- A GUI
- Networking


You may notice that, since this is a tutorial, we haven't yet discussed which kind
of kernel we will write. It will probably be a monolithic one since they are easier
to design and implement, and after all this is our first OS. Maybe in the future
we'll add a lesson "15-b" with a microkernel design. Who knows.