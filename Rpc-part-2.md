# RPC part 2

## Intro to Apache Thrift

So now that we have a basic understanding of the theory behind RPC lets work on a mini-application where we could practice some of the things we learned. In this tutorial, we will be using [Apache Thrift] (https://thrift.apache.org/), as our IDL and data serilizer. Not because it's the best or the worst, but because it's one of the most popular and well documented solutions out there.

So lets get started.

In this tutorial we're going to write a simple service that takes in an image and returns a list of tags related for that image. For the sake of simplicity, we won't be doing any image analysis ourselves and will be using an [external API] (clarifai.com) to do so. This is more of an exercise on transfering data with thrift.

First thing you're going to want to do is download Apache Thrift! You can do that by clicking [this link] (https://thrift.apache.org/docs/install/) and following the instructions for your operating system. 

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

```
$ ls -R gen-go/
service

gen-go//service:
constants.go        make_tags-remote    maketags.go     ttypes.go

gen-go//service/make_tags-remote:
make_tags-remote.go
```

The next step is to actually write some code to perform the remote procedure call. 

First thing we need to do is write a client that will be performing this call, we'll call this `client.go` 

```go
package main

import (
	"fmt"
	"git.apache.org/thrift.git/lib/go/thrift"
	"github.com/faiq/gen-go/service"
	"io/ioutil"
	"os"
	"path/filepath"
)

func main() {
	socket, err := thrift.NewTSocket("localhost:8000")
	if err != nil {
		fmt.Printf("There was an error creating your socket! Here it is %v", err)
	}
	protocolFactory := thrift.NewTBinaryProtocolFactory()
	client := service.NewMakeTagsClientFactory(socket, protocolFactory)
	pwd, _ := os.Getwd()
	fileName := pwd + "/img.png"
	fileName, _ = filepath.Abs(fileName)
	imgBytes, err := ioutil.ReadFile(fileName)
	if err != nil {
		fmt.Printf("There was an err reading the file! Here it is %v", err)
	}
	tags, err := client.Generate(service.Image(imgBytes))
	fmt.Printf("These are the tags for your image %v", tags)
	socket.Close()
}
``` 
