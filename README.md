# Libft project for 42

Libft is the first project for the 42 Common Core Curriculum. It consists in the creation of a library with several functions that will be used in later projects.

---

## IMPORTANT CONCEPTS:

### - Library:

A library is a key concept in programming — almost all programming languages work with libraries. The term "library" is perfect to give the idea of what it does; in fact, a library is nothing more than a collection of functions (the books) that your program can use when needed, exactly like how you can go to the public library and borrow a book from it.

A library can be either **STATIC** or **DYNAMIC**. What's the difference?

The difference is quite simple — it all depends on **where** the code from the library is stored.

---

## STATIC LIBRARY – file extension `.a` on Linux, `.lib` on Windows

The code of the library is directly included in your executable file. This means that your executable will have the binary code for the functions of the library.  
As you might have guessed, this results in a bigger executable file, but the benefit is that you'll have less overhead when calling library functions.

### To create a static library, there are two steps:

1. **Create the object files for the different functions of the library with the `-c` flag**  
   ```bash
   gcc -c foo.c  # This creates the object file foo.o
   ```

2. **Create the library with the `ar` command**  
   ```bash
   ar rcs libft.a foo.o  # This creates the static library libft.a
   ```

Great! The library was created. Now the last step is linking it with your program:

```bash
gcc main.c -L/path/to/your/library -lft
```

This one is a little bit tricky because to tell the compiler where to search for your library, you have to provide the path using the `-L` flag. By default, `gcc` will check for libraries in `/usr/lib/x86_64-linux-gnu/` (where common libraries like `libc` and `libm` are).

The `-l` flag tells the compiler which file is your library. Keep in mind that this file is supposed to be called `libACTUALNAME.a`, and for this reason, when using the `-l` flag you only need to provide the actual name of your library (e.g., `ft` in this case). The `lib` prefix and `.a` extension are automatically added by the compiler when searching for the file.  
That's why you write `-lft` and not `-llibft.a` or `-llibft`.

When the `a.out` file is created with this command, it will include the binary of the library.

It’s worth noting that since the executable contains the binary of all the functions of the library, your program is totally independent from the library file.  
What does this mean?  
Basically, it means that to run the program, you only need the program — there’s no need to also have the library file on the computer. This concept will be even more clear when looking at Dynamic Libraries.

---

## DYNAMIC LIBRARY – file extension `.so` on Linux, `.dll` on Windows

The code of the library is kept **separate** from your executable. This means that there are some extra steps when calling a function from the library you're using. Let’s take a look.

Every OS has some libraries that are part of it. As said before, in Linux you can find libraries available to your programs in `/usr/lib/x86_64-linux-gnu`.  
Here you’ll find various libraries — the most important one is `libc`, the "god library" that contains the most common C functions.

Ever wondered where functions like `malloc`, `free`, etc., are defined? They are part of this library.

Since the code is not part of your executable but is stored somewhere in computer memory, your program has to know where to go when a function from the library is called.

At execution time, the computer will locate the memory address of the function and jump to it to execute it.

To achieve this, there’s something called the **GOT (Global Offset Table)** that is part of your executable. It stores the memory addresses of the functions you are looking for.

So when you call, let’s suppose, `printf`, what happens under the hood is: during execution, the computer checks the GOT for the memory address of `printf`. Once located, it jumps to that address. If it’s unable to find it, you’ll get an error saying it wasn’t possible to locate `printf`.

> (There’s an extra layer called the **PLT (Procedure Linkage Table)** between your program and the GOT, but we won’t go into that here.)

---

### To create a dynamic library, you need to go through 2 steps:

1. **Create the object files with Position Independent Code (P.I.C)**  
   ```bash
   gcc -fPIC -c foo.c bar.c  # This creates foo.o and bar.o with PIC
   ```

2. **Create the shared object with the `-shared` flag**  
   ```bash
   gcc -shared -o libft.so foo.o bar.o
   ```

As you can see, the process is quite similar to creating a static library. The main difference is that you use the `-fPIC` flag when creating the objects and the `-shared` flag to link them together in the `libft.so` library (`.so` stands for "shared object").

To use the dynamic library, the command is the same as before:

```bash
gcc main.c -L/path/to/your/library -lft
```

---

What’s the main difference here?

If you check the size of your executable, you’ll notice it's much smaller than the one statically linked. That’s because your executable doesn’t contain the actual code of the library functions — instead, it holds a bunch of memory references.

This is great!  
But here’s the tradeoff: your program is no longer independent from the library. Since your program refers to a memory location outside itself, the library must be present in memory when the program runs.

That means if you want to run your program on another machine, you must **also provide the `.so` file** you're using.

---

Also, it’s worth noting that by default, `gcc` will link some libraries to your executable. (You can disable this with the `-nostdlib` flag during compile time.)

You can check which libraries are linked by doing:

```bash
gcc main.c
ldd a.out
```

You’ll usually see an output like this:

```
linux-vdso.so.1 =>  (0x000073a087b91000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x000073a087800000)
/lib64/ld-linux-x86-64.so.2 (0x000073a087b93000)
```

`libc` is indeed the standard C library — that’s why you can use functions like `malloc`, `free`, and `printf` without explicitly linking anything at compile time (the compiler does it for you!).

However, if you want to use functions from the math library, you need to compile with the `-lm` flag.

Quite interesting, uh?

---

That’s it!  
If you find any mistakes or typos, don’t hesitate to reach out!

