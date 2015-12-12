# Intro to Services Using Apache Thrift and Go

In my previous post I talked about the theory behind RPC, but what can we accomplish with it? How can we use it in our applications? How do we use it in our applications. These are all great questions to have. One popular use case for using RPC is implementing a Service Oriented Architecture (SOA). People have written books about Service Oriented Architecture, but to give you a general idea of what SOA is, lets break it down piece by piece. A Service is a contained program that accomplishes a certain task. For example, you can have a service to encrypt all of the data that you send it or you can have a service that checks a user's credentials. Therefore, a Service Oriented Architecture is a pattern of designing your systems that's based around having services that carry out specefic tasks. I strongly recommend that you check out some [of](http://blog.yohanliyanage.com/2012/03/getting-soa-right-thinking-beyond-the-right-angles/) [these](http://searchsoa.techtarget.com/definition/service-oriented-architecture) [awesome](http://stackoverflow.com/questions/2026523/what-is-soa-in-plain-english) links.

What does SOA have to do with RPC? Services are functions that you execute on a remote server, and that's exactly what a Remote Procedure Call is.

[Thrift](http://thrift.apache.org/) is an RPC framework that provides a language for you to write out Interfaces for services, and offers several different ways of encoding and transfering your data. The major benefits of using Thrift

In this post, we're going to write a simple service in Go that uses the Thrift framework that takes in an image, and returns a list of tags related for that image. 

For the sake of simplicity, we won't be doing any image analysis ourselves and will be using [clarifai] (http://clarifai.com) to do so. If you're not familiar with it, Clarifai is an awesome service that provides tags for a provided image. This post is more of an exercise on transferring data and writing serices with Thrift, rather than the image analysis.

First you're going to want to do is downloading our requirements:

1. [Apache Thrift] (https://thrift.apache.org/docs/install/) 
2. [Golang] (https://golang.org/dl/) 

If you haven't done so already, you should [set up your go environment](https://golang.org/doc/code.html#Organization) 

If you don't want to follow along and skip straight to the code you can find it on my [GitHub](https://github.com/faiq/intro-to-rpc)

## Setting Up Your Thrift File

Next thing you're going to want to do create a `.thrift` file. This file is where you're going to write the interface defintions for all your services and types so we could create langugae specific source code for doing RPC. 

```thrift
    typedef binary image // here we are creating our own type, for readability, "image" which is an array of bytes
    
    service MakeTags {
            list<string> generate(1: image img) // here we are defining a service that has a method generate which returns a list of strings and takes in an image called img
    }
```

Now that we have our services in our Interface Defintion Language, lets compile it so we could use it in our code. In this example, I'll be using Golang, but thrift supports tons of languages so pick your favorite!

To do this we'll use the thrift command line tool that we downloaded earlier.

`thrift -r --gen go service.thrift`

This should generate the following file tree

```sh
$ ls -R gen-go/
service

gen-go//service:
constants.go        make_tags-remote    maketags.go     ttypes.go

gen-go//service/make_tags-remote:
make_tags-remote.go
```

## Writing an RPC Client 

The next step is to actually write some code to perform the remote procedure call from a client to a remote server that's going to extract the tags from a picture. The concept behind it is very similar to how we make requests from our front end web applications to our servers, but there's a few more steps involved. Let's look at some code.
 
```go
package main

import (
	"fmt"
	"git.apache.org/thrift.git/lib/go/thrift"
	"github.com/faiq/intro-to-rpc/gen-go/service"
	"io/ioutil"
	"os"
	"path/filepath"
)

func main() {
	socket, err := thrift.NewTSocket("localhost:8090")
	if err != nil {
		fmt.Printf("There was an error creating your socket! Here it is %v", err)
		os.Exit(1)
	}
	transport := thrift.NewTBufferedTransport(socket, 1024)
	protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()
	client := service.NewMakeTagsClientFactory(transport, protocolFactory)

	pwd, _ := os.Getwd()
	fileName := pwd + "/img.png"
	fileName, _ = filepath.Abs(fileName)
	imgBytes, err := ioutil.ReadFile(fileName)
	if err != nil {
		fmt.Printf("There was an err reading the file! Here it is %v", err)
		os.Exit(1)
	}

	socket.Open()
	tags, err := client.Generate(service.Image(imgBytes))
	if err != nil {
		fmt.Printf("There was an err getting the tags! Here it is %v ", err)
		os.Exit(1)
	}
	fmt.Printf("These are the tags for your image %v", tags)
	socket.Close()
}
``` 

A lot of stuff is happening here, so lets go over it line by line.

On the first line we're creating a new TCP socket to connect to where our image tagging service is. In this example, our service is on the same machine, but that's likely to change in a production enviornment. This is the lowest level in the thrift networking stack.

On line next line after the error check, we're going to set a buffereing layer on top of the network socket, so we can read/write chunks of the buffer size from the socket at a time. This is done to increase performance over raw sockets.

The next level of the thrift networking stack is the protocol. A protocol is a set of rules and conventions for computers to follow when reading and writing the data to and from the sockets. Now the computers that are communicating will know how to deal with the data they're sending each other. In this case, we're using a Binary Protocol to send our data across the wire.

In the following line, we're going to make a new thrift client with the transfer and protocol layers that we defined. This is the highest part of the thrift stack and it's the only part that we interact with.

Here's a picture of the full stack!

<img src="https://upload.wikimedia.org/wikipedia/en/d/df/Apache_Thrift_architecture.png"/>

The next few lines are specific to this example. In them, we are opening a file called "img.png", and reading it's contents into memory.

On the next line we're going to open up the socket that we created, so we can start communicatting with our external service.

Now that there's a an open connection between the RPC server and client, we're able to call the Generate function that we defined in our .thrift file. If there are no errors, you would have made your first remote procedure call using thrift! 

Finally, we print out the tags we got for our image and close the socket that we opened.

It's really important to note that you're not bound to using thrift the way that I used it. You have the choice between several different types of transportation and protocols. You can read more about the different option availble to you in this [awesome article](http://thrift-tutorial.readthedocs.org/en/latest/thrift-stack.html).
