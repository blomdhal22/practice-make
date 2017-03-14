# Built-in macros
# $@ : the name of the file to be ``made''
# $* : the shared prefix of the target and dependent - only for suffix rules
# $< : the name of the related file that caused the action (the precursor to the target) - this is only for suffix rules
# $? : the set of dependent names that are younger than the target
# $$ : escapes macro substitution, returns a single ``$''.
# $^ : 

OBJS = test1.o test2.o test3.o test4.o

test: $(OBJS)
	gcc -o $@ $^

# suffix rule
.c.o:
	gcc -c $(CFLAGS) $<

clean:
	rm $(OBJS) test

# test
# (shell)$ make CFLAGS="-g"