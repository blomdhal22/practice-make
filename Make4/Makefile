# create executable file
test: test1.o \
      test2.o \
      test3.o \
	gcc -o test test1.o test2.o test3.o

test4:
	cd ../; \
	ls -al; \
	pwd
	cp t3.o test4.o

# fake target
clean:
	rm test1.o test2.o test3.o

# create dependency
dep:
	gccmakedep test1.c test2.c test3.c
# DO NOT DELETE
test1.o: test1.c a.h
test2.o: test2.c b.h
test3.o: test3.c b.h c.h
