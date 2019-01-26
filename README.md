# libprocesshider


Hide a process under Linux using the LD Preloader.

Full tutorial available at https://sysdigcloud.com/hiding-linux-processes-for-fun-and-profit/

## Usage

In short, first modify [`processhider.c`](https://github.com/artikrh/libprocesshider/blob/master/processhider.c#L9) to set a process name of your liking and then compile the library as a shared object:

```
$ mkdir {32,64}
$ gcc -Wall -m32 -fPIC -shared -o 32/libprocesshider.so processhider.c -ldl
$ gcc -Wall -fPIC -shared -o 64/libprocesshider.so processhider.c -ldl
$ sudo mv 32/libprocesshider.so /usr/lib32/libprocesshider.so
$ sudo mv 64/libprocesshider.so /usr/lib/x86_64-linux-gnu/libprocesshider.so
```

Note: Since we are going to use `$LIB` environment variable, the above `$LIB` shell expansion to `lib32` for 32-bit binaries and `lib/x86_64-linux-gnu` for 64-bit is valid for Debian based distributions only. For other Linux systems, you can intercept the right `$LIB` expansion using the `strace` tool - more specificaly, the `openat()` system call (as others may use `lib64` for 64-bit and `lib` for 32-bit executables).

Load it with the global dynamic linker:

```
$ sudo echo '/usr/$LIB/libprocesshider.so' > /etc/ld.so.preload
```

And your process will be off the radar from tools such as `ps`.
