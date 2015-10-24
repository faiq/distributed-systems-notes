# Remote Procedure Calls

Sockets are awesome but they only provide read and write capabilites to another computer, what if we want to do more? When writing a program we're usually making function calls to manipulate data, so how do we get a similar experience with a distributed system?

Answer: The Remote Procedure Call

The basic flow of a Remote Procedure Call goes like this:

1. There are two machines, Machine 1 and Machine 2
2. Some process, lets call it process A is running on Machine 1
3. In order to complete it's task it must call some procedure that lives on Machine 2
~ Here's where the magic of RPC happens ~
4. Process A can call this procedure on machine 2, and suspend it's processing
5. On Machine 2 execution of the given procedure begins, and when it's done a return value is passed to Process A.
6. When Process A gets the return value it resumes execution as normal

The flow of execution here is similar to any other program, except for the tiny fact that you're doing it across TWO DIFFERENT machines! Seriously, how cool is that?

## How does a Remote Procedure Call differ from a local procedure call?

Local procedure calls are supported by a [call stack] (http://www.cs.umd.edu/class/sum2003/cmsc311/Notes/Mips/stack.html). Just for some context, a stack is a contiguous section of memory where all of your functions' local variables are managed. Once a function is completed they return a value back to the caller and get cleared off the stack.

This paradigm works on a local machine, because of the idea of shared state being held in the stack, but doesn't work well when you're working with different machines across a network. We will use tools such as sockets and local procedure calls, in order to emulate the kind of programming experiece we have with local procedure calls.

## How is a remote procedure call implemented? 

The backbone behind the remote procedure call are stub functions. On the client end, a stub function will look like the funciton they're trying to call, but will actually contain code for communicating messages over the network.

On the client end, the stub function is responsible for taking all the proper paramteters, converting them into an array of bytes (perhaps according to a specific format), and send this information through a socket. 

On the other end, a server unpacks all of the arguments from byte form, to a form that it can use to perform operations with. It then calls the function that performs the operation that the client needs. Once the operation is complete, it returns values back to the server stub. Then the server stub converts the return values (again perhaps according to some specific format) and sends them over the network. 

Back on the client end, the client stub reads the messages from the local kernel, converts it to a representation that the client could use, and returns this value to the client code.

## Implementation specifics

Questions to consider when implementing remote procedure calls

1. How should I pass parameters? 
2. How should data be represented?
3. How should I transfer my data?
4. What ports should you bind to, and how do you manage hosts?
5. How should I handle errors?
6. How should I detect errors?

These answers are usually answered by an Interface Definition Language or IDL. Think of an IDL as a compiler for generating the stub functions that address the above questions. This allows the programmer to focus on writing out the actual logic, as opposed to having to worry about implementing logic for things like marshalling data and configuring ports.
