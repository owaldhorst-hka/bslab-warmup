helloworld: helloworld.o printhelloworld.o
	g++ helloworld.o printhelloworld.o -o helloworld
	
helloworld.o: helloworld.cc
	g++ -c helloworld.cc
	
printhelloworld.o: printhelloworld.cc
	g++ -c printhelloworld.cc
	
clean:
	rm -f helloworld *.o