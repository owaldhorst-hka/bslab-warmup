CPPFLAGS = -Wall -g
LINKFLAGS = -lc

PROGS = openandread write printargs copy writeandreadstructs

OPENANDREAD_OBJS = openandread.o
WRITE_OBJS = write.o
PRINTARGS_OBJS = printargs.cc
COPY_OBJS = copy.o
WRITEANDREADSTRUCTS_OBJS = writeandreadstructs.o

all: $(PROGS)

openandread: $(OPENANDREAD_OBJS)
	g++ $(LINKFLAGS) $^ -o $@

write: $(WRITE_OBJS)
	g++ $(LINKFLAGS) $^ -o $@

copy: $(COPY_OBJS)
	g++ $(LINKFLAGS) $^ -o $@

printargs: $(PRINTARGS_OBJS)
	g++ $(LINKFLAGS) $^ -o $@

writeandreadstructs: $(WRITEANDREADSTRUCTS_OBJS)
	g++ $(LINKFLAGS) $^ -o $@
		
%.o: %.cc
	g++ $(CPPFLAGS) -c $<
	
clean:
	rm -f $(PROGS) *.o
