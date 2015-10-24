# RPC part 2

## Intro to Apache Thrift

So now that we have a basic understanding of the theory behind RPC lets work on a mini-application where we could practice some of the things we learned. In this tutorial, we will be using [Apache Thrift] (https://thrift.apache.org/), as our IDL and data serilizer. Not because it's the best or the worst, but because it's one of the most popular and well documented solutions out there.

So lets get started.

In this tutorial we're going to write a simple service that takes in an image and returns a list of tags related for that image. For the sake of simplicity, we won't be doing any image analysis ourselves and will be using an [external API] (clarifai.com) to do so. This is more of an exercise on transfering data with thrift.

First thing you're going to want to do is download Apache Thrift! You can do that by clicking (this link) [https://thrift.apache.org/docs/install/] and following the instructions for your operating system. 

Next thing you're going to want to do create a `.thrift` file. This file is where you're going to write the interface defintions for all your services and types so we could create langugae specific source code for doing RPC. 

```thrift
    typedef binary image // here we are creating our own type, for readability, "image" which is an array of bytes
    
    service MakeTags {
            list<string> generate(1: image) // here we are defining a service that has a method generate which returns a list of strings and takes in an image
    }
```
