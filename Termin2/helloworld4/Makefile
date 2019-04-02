CPPFLAGS = -Wall -g
LINKFLAGS = -lm

OBJS = helloworld.o printhelloworld.o

helloworld: $(OBJS)
	g++ $(LINKFLAGS) $^ -o helloworld
	
%.o: %.cc
	g++ $(CPPFLAGS) -c $<
	
clean:
	rm -f helloworld *.o
