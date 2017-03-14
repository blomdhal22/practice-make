# Built-in macros
# $@ : the name of the file to be ``made''
# $* : the shared prefix of the target and dependent - only for suffix rules
# $< : the name of the related file that caused the action (the precursor to the target) - this is only for suffix rules
# $? : the set of dependent names that are younger than the target
# $$ : escapes macro substitution, returns a single ``$''.
# $^ : 

OB = test1.o test2.o
OBJS = $(OB) test3.o

# raise : *** Recursive variable `OBJS' references itself (eventually).  Stop.
#OBJS = $(OBJS) test4.o

# use ':=' to avoid recursive
OBJS := $(OBJS) test4.o

# substitution
SRC = $(OBJS:.o=.c)

# SRC=test1.c test2.c test3.c test4.c
#$(error SRC=$(SRC))

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

test4.o: test4.c b.h c.h

# fake target
# '-' means ignore error
# ex: "make: [clean] Error 1 (ignored)"
clean:
	-rm $(OBJS)