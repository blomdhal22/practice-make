# MakeFile

referenced from : 김종훈 외, 초보자를 위한 Linux & Unix C Programming

## Basics

### Grammar

```
Target: dependency_file1 dependency_file2 dependency_file3
    <tab>command
```

example:

```
test: test.c
	gcc -o test test.c

clean:
	rm test.o
```

### example

```
// test1.c 
#include "a.h"

extern void func1();
extern void func2();

int main(int argc, char *argv[]) {

    //printf("test1.c");
    func1();
    func2();

    return 0;
}
```

```
// test2.c 
#include "b.h"

void func1() {
    //printf("test2\n");
}
```

```
// test3.c 
#include "b.h"
#include "c.h"

void func2() {
    //printf("test3\n");
}
```

```
$ touch a.h b.h c.h
```

Result:

```
$ make
gcc -c test1.c
gcc -c test2.c
gcc -c test3.c
gcc -o test test1.o test2.o test3.o
```

### Comments

Comments : start with '#'

```
# create executable file
test: test1.o test2.o test3.o
	gcc -o test test1.o test2.o test3.o

# fake target
clean:
	rm test1.o test2.o test3.o

# create dependency
dep:
	gccmakedep test1.c test2.c test3.c

```

### Generate dependency by automatically

```
$ make dep
```

Result

```
...<skip>
# create dependency
dep:
	gccmakedep test1.c test2.c test3.c

# DO NOT DELETE
test1.o: test1.c a.h
test2.o: test2.c b.h
test3.o: test3.c b.h c.h
```

**NOTE:**

1. when there is not gccmakedep, install xutils-dev
2. another option is using gcc -M option

> $ sudo apt-get install xutils-dev

### Using shell and multiple line

1. Using shell, end with ';'
2. Multiple line, end with '\\'

```
test4:
	cd ../; \
	ls -al; \
	pwd
	cp t3.o test4.o
```

### Ignore command fail

start command with '-'

```
# fake target
# '-' means ignore error
# ex: "make: [clean] Error 1 (ignored)"
clean:
	-rm $(OBJS)
```

Result

```
rm test1.o test2.o test3.o test4.o
rm: cannot remove `test1.o': No such file or directory
rm: cannot remove `test2.o': No such file or directory
rm: cannot remove `test3.o': No such file or directory
rm: cannot remove `test4.o': No such file or directory
make: [clean] Error 1 (ignored)
```

## Macro

Grammar

```
# define
NAME = value

# use
$(NAME)
```

### Built-in macros

```
# Built-in macros
# $@ : the name of the file to be ``made''
# $* : the shared prefix of the target and dependent - only for suffix rules
# $< : the name of the related file that caused the action (the precursor to the target) - this is only for suffix rules
# $? : the set of dependent names that are younger than the target
# $$ : escapes macro substitution, returns a single ``$''.
# $^ : all condition files of dependency

OBJS = test1.o test2.o test3.o

# gcc -o test test1.o test2.o test3.o
test: $(OBJS)
	gcc -o $@ $^

# gcc -c test1.c
test1.o: test1.c a.h
	gcc -c $<

# gcc -c 'test2''.c'
test2.o: test2.c a.h b.h
	gcc -c $*.c

# gcc -c 'test3''.c'
test3.o: test3.c b.h c.h
	gcc -c $*.c

# fake target
clean:
	rm $(OBJS)
```

### Recursive Macro

```
OB = test1.o test2.o
OBJS = $(OB) test3.o

# raise : *** Recursive variable `OBJS' references itself (eventually).  Stop.
OBJS = $(OBJS) test4.o
```

### Substitution

```
OB = test1.o test2.o
OBJS = $(OB) test3.o

# use ':=' to avoid recursive
OBJS := $(OBJS) test4.o

# substitution
SRC = $(OBJS:.o=.c)

# SRC=test1.c test2.c test3.c test4.c
#$(error SRC=$(SRC))
```

## Rules

### Inference rules

Even though There are not TARGET of test1.o test2.o test3.o and test4.o, Operation is well.

```
OBJS = test1.o test2.o test3.o test4.o

test: $(OBJS)
	gcc -o $@ $^

# fake target
# '-' means ignore error
# ex: "make: [clean] Error 1 (ignored)"
clean:
	-rm $(OBJS) test
```

Print database

> $ make -p

### Suffix rule

```
OBJS = test1.o test2.o test3.o test4.o

test: $(OBJS)
	gcc -o $@ $^

# suffix rule
# make .c files into .o files
# $< means file name
# $(CFLAGS) means compiler flags which be pre-defined.
.c.o:
	gcc -c $(CFLAGS) $<
```

Result

```
$ make CFLAGS="-g"
gcc -c -g test1.c
gcc -c -g test2.c
gcc -c -g test3.c
gcc -c -g test4.c
gcc -o test test1.o test2.o test3.o test4.o
```

### Pattern rule

```
# pattern rule
OBJS = test1_d.o test2_d.o test3_d.o test4_d.o

test: $(OBJS)
	gcc -o $@ $(OBJS)

# pattern rule
# % means all
%_d.o: %.c
	gcc -c -g $< -o $@
```

Result

```
gcc -o test test1_d.o test2_d.o test3_d.o test4_d.o
```

## Options

> $ make -#

```
Usage: make [options] [target] ...
Options:
  -b, -m                      Ignored for compatibility.
  -B, --always-make           Unconditionally make all targets.
  -C DIRECTORY, --directory=DIRECTORY
                              Change to DIRECTORY before doing anything.
  -d                          Print lots of debugging information.
  --debug[=FLAGS]             Print various types of debugging information.
  -e, --environment-overrides
                              Environment variables override makefiles.
  -f FILE, --file=FILE, --makefile=FILE
                              Read FILE as a makefile.
  -h, --help                  Print this message and exit.
  -i, --ignore-errors         Ignore errors from commands.
  -I DIRECTORY, --include-dir=DIRECTORY
                              Search DIRECTORY for included makefiles.
  -j [N], --jobs[=N]          Allow N jobs at once; infinite jobs with no arg.
  -k, --keep-going            Keep going when some targets can't be made.
  -l [N], --load-average[=N], --max-load[=N]
                              Don't start multiple jobs unless load is below N.
  -L, --check-symlink-times   Use the latest mtime between symlinks and target.
  -n, --just-print, --dry-run, --recon
                              Don't actually run any commands; just print them.
  -o FILE, --old-file=FILE, --assume-old=FILE
                              Consider FILE to be very old and don't remake it.
  -p, --print-data-base       Print make's internal database.
  -q, --question              Run no commands; exit status says if up to date.
  -r, --no-builtin-rules      Disable the built-in implicit rules.
  -R, --no-builtin-variables  Disable the built-in variable settings.
  -s, --silent, --quiet       Don't echo commands.
  -S, --no-keep-going, --stop
                              Turns off -k.
  -t, --touch                 Touch targets instead of remaking them.
  -v, --version               Print the version number of make and exit.
  -w, --print-directory       Print the current directory.
  --no-print-directory        Turn off -w, even if it was turned on implicitly.
  -W FILE, --what-if=FILE, --new-file=FILE, --assume-new=FILE
                              Consider FILE to be infinitely new.
  --warn-undefined-variables  Warn when an undefined variable is referenced.

This program built for x86_64-pc-linux-gnu
Report bugs to <bug-make@gnu.org>
```

### -f option

Default makefile name is one of the GNUmakefile, makefile, Makefile

Read file as makefiles

> $ make -f Mymakefile

### -n, -W option

-n : just print them, don't run any commands
-W : Consider FILE to be infinitely new.

> $ make -n -W test3.c

```
gcc -c  test1.c
gcc -c  test2.c
gcc -c  test3.c
gcc -c  test4.c
gcc -o test test1.o test2.o test3.o test4.o
```

### -s option

-s : silent, don't echo commands

> $ make -W test3.c -s
> $

### -r option

-r : disable the built-in implicit rule

```
# pattern rule
OBJS = test1_d.o test2_d.o test3_d.o test4_d.o

test: $(OBJS)
	gcc -o $@ $(OBJS)

# pattern rule
# % means all
%_d.o: %.c
	gcc -c -g $< -o $@

clean:
	rm $(OBJS) test
```

> $ make -r

```
make: *** No rule to make target `test1.o', needed by `test'.  Stop.
```

### -d option

-d : Print lots of debugging information.

> $ make -d clean

```
GNU Make 3.81
Copyright (C) 2006  Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.

This program built for x86_64-pc-linux-gnu
Reading makefiles...
Reading makefile `Makefile'...
Updating makefiles....
 Considering target file `Makefile'.
  Looking for an implicit rule for `Makefile'.
  Trying pattern rule with stem `Makefile'.
  Trying implicit prerequisite `Makefile.o'.
```

### -k option

-k : keep going when some targets can't be made