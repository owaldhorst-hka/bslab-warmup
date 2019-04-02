BIN= helloworld
OBJS = helloworld.o printhelloworld.o

$(BIN): $(OBJS)
	g++ $^ -o helloworld
	
%.o: %.cc
	g++ -c $<
	
clean:
	rm -f helloworld *.o
