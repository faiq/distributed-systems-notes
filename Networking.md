# Introduction

Unlike parallel systems, we need independent mahcines working together without shared memory- and the only way to do this is through ~the internet~

# Modes of connection

1. circut switched - there exists a dedicated channel between you and the machine you're trying to connect to. Here you have full bandwidth of the circut.

2. packet switched - shared connections, rather than a dedicated one. So we break the data we're sending into "Packets"- which contain a chunck of the original data, along with the destination of where it's going. Now you're able to send information from one computer to another using shared connections, but the bandwidth is going to be less than the full capacity of the network because it's shared.

# So how do computers understand each other?

So now that we have some method of transportation from getting some data from one computer to another, how does a computer turn this information into something actionable? How does it interprate the data in that package? To do this computers use protocols. 

In english a protocol is defined as - "the official procedure or system of rules governing affairs of state or diplomatic occasions."

A protocol is a set of rules and conventions for computers to follow.

Network protocols (rules for the network) are generally organized in layers, so you can replace one layer without having to change other layers. The most popular guideline of layering these networks is called the OSI Model.

## OSI model

<a><img src="http://f.tqn.com/y/compnetworking/1/S/g/basics_osimodel.jpg"></a>

Lets look at where these pieces fit in on a Network.

The network we're going to be looking at is a Local Area Network, which typically covers a small area (a few rooms or buildings).

Lets start from the bottom of the OSI model, where the roads of the network are established

1. The computer needs some sort of hardware interface for it to talk to the Network- these are called Network Interface Cards.

2. There needs to be some sort of physical medium that connects all the devices together, typically wires are used but it can also be wireless.

Some examples of Media:
- Shieded/Unshield Twisted Pair Wires
- Coax cables
- Fiber 
- Wireless 

3. A hub is a central access point for the computers to the Local Network and is the bottom most layer on the OSI model. 

4. Bridges connect different LAN segments together, Layer 2 of the OSI model.

5. Routers sit on the gateway of networks and determine the next point where a packet should go.

## Internet Protocol

The internet protocol (IP) connects together different machines across seperately connected networks. It routes a packet from one physical network to another.

In the internet protocol every machine is given a 32 bit unique address- which means there are a total of over 4 billion unique addresses! 

So how do we keep track of the massive amounts of addresses? Through establishing a hierarchy such that machines close to each other have common prefixes.

The address is broken up into two parts:

network number — identifies the network that the machine belongs to
host number — identifies a machine on that network.

So now routers can keep track of a smaller number of network numbers and forward traffic for hosts outside of thier network. However there are many small networks and few big ones. This means large networks need more space in the address for the host number and smaller ones don't need as much. So networks are split up into different classes.

## How IP relates to the OS

Operating systems have an IP driver which control thigns like: getting the maximum packet size for the current computer, routing from one address to another, breaking packets up, recieve data from the driver.

This device driver that controls the network interface card process getting packets and sending them to the IP driver.And send the packets from the ip driver to the hardware so it can make it's long difficult journey through the internet.

Computers dont know about thier IP addresses, so they use an Address Resolution Protocol to convert IP addresses in packets to local device.

## Protocols over IP

1. TCP - (connection orriented) creates a virtual connection which involves sending acknowledgement packets, checksums to validate data. ensures data arrives in sequence.

2. UDP - connectionless protocol - data may arrive out of sequence and might be lost an address must be specified in every request.

We interface with these in our software through sockets.
