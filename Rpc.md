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

The flow of execution here is similar to any other program, but for the tiny fact that you're doing it across TWO DIFFERENT machines! Seriously, how cool is that?

## How does a Remote Procedure Call differ from a local procedure call?

Local procedure calls are supported by a [call stack] (http://www.cs.umd.edu/class/sum2003/cmsc311/Notes/Mips/stack.html). Just for some context, a stack is a contiguous section of memory where all of your functions' local variables are managed. Once a function is completed they return a value back to the caller and get cleared off the stack.

This paradigm works on a local machine, because of the idea of shared state being held in the stack, but doesn't work well when you're working with different machines across a network.

