Network applications are everywhere. Any time you browse the Web, send an email message, or play an online game, you are using a network application. Interestingly, all network applications are based on the same basic programming model, have similar overall logical structures, and rely on the same programming interface.

Network applications rely on many of the concepts that you have already learned in our study of systems. For example, processes, signals, byte ordering, memory mapping, and dynamic storage allocation all play important roles. There are new concepts to master as well. You will need to understand the basic client-server programming model and how to write client-server programs that use the services provided by the Internet. At the end, we will tie all of these ideas together by developing a tiny but functional Web server that can serve both static and dynamic content with text and graphics to real Web browsers.

## 11.1 The Client-Server Programming Model

Every network application is based on the client-server model. With this model, an application consists of a server process and one or more client processes. A server manages some resource, and it provides some service for its clients by manipulating that resource. For example, a Web server manages a set of disk files that it retrieves and executes on behalf of clients. An FTP server manages a set of disk files that it stores and retrieves for clients. Similarly, an email server manages a spool file that it reads and updates for clients.

The fundamental operation in the client-server model is the transaction (Figure 11.1). 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659054733672.png" style="zoom: 40%" />

A client-server transaction consists of four steps:

1. When a client needs service, it initiates a transaction by sending a request to the server. For example, when a Web browser needs a file, it sends a request to a Web server.
2. The server receives the request, interpret sit, and manipulates its resources in the appropriate way. For example, when a Web server receives a request from a browser, it reads a disk file.
3. The server sends a response to the client and then waits for the next request. For example, a Web server sends the file back to a client.
4. The client receives the response and manipulates it. For example, after a Web browser receives a page from the server, it displays it on the screen.

It is important to realize that clients and servers are processes and not machines, or hosts as they are often called in this context. A single host can run many different clients and servers concurrently, and a client and server transaction can be on the same or different hosts. The client-server model is the same, regardless of the mapping of clients and servers to hosts.

## 11.2 Networks

Clients and servers often run on separate hosts and communicate using the hardware and software resources of a computer network. Networks are sophisticated systems, and we can only hope to scratch the surface here. Our aim is to give you a workable mental model from a programmer’s perspective.

To a host, a network is just another I/O device that serves as a source and sink for data, as shown in Figure 11.2.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659054809504.png" style="zoom: 50%" />

An adapter plugged into an expansion slot on the I/O bus provides the physical interface to the network. Data received from the network are copied from the adapter across the I/O and memory buses into memory, typically by a DMA transfer. Similarly, data can also be copied from memory to the network.

Physically, a network is a hierarchical system that is organized by geographical proximity. At the lowest level is a LAN (local area network) that spans a building or a campus. The most popular LAN technology by far is Ethernet, which was developed in the mid-1970s at Xerox PARC. Ethernet has proven to be remarkably resilient, evolving from 3 Mb/s to 10 Gb/s.

An Ethernet segment consists of some wires (usually twisted pairs of wires) and a small box called a hub, as shown in Figure 11.3. Ethernet segments typically span small areas, such as a room or a floor in a building. Each wire has the same maximum bit bandwidth, typically 100 Mb/s or 1 Gb/s. One end is attached to an adapter on a host, and the other end is attached to a port on the hub. A hub slavishly copies every bit that it receives on each port to every other port. Thus, every host sees every bit.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659054858203.png" style="zoom: 40%" />

Each Ethernet adapter has a globally unique 48-bit address that is stored in a nonvolatile memory on the adapter. A host can send a chunk of bits called a frame to any other host on the segment. Each frame includes some fixed number of header bits that identify the source and destination of the frame and the frame length, followed by a payload of data bits. Every host adapter sees the frame, but only the destination host actually reads it.

Multiple Ethernet segments can be connected into larger LANs, called bridged Ethernets, using a set of wires and small boxes called bridges, as shown in Figure 11.4. Bridged Ethernets can span entire buildings or campuses. In a bridged Ethernet, some wires connect bridges to bridges, and others connect bridges to hubs. The bandwidths of the wires can be different. In our example, the bridge–bridge wire has a 1 Gb/s bandwidth, while the four hub–bridge wires have bandwidths of 100 Mb/s.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659054918444.png" style="zoom: 50%" />

Bridges make better use of the available wire bandwidth than hubs. Using a clever distributed algorithm, they automatically learn over time which hosts are reachable from which ports and then selectively copy frames from one port to another only when it is necessary. For example, if host `A` sends a frame to host `B`, which is on the segment, then bridge `X` will throw away the frame when it arrives at its input port, thus saving bandwidth on the other segments. However, if host `A` sends a frame to host `C` on a different segment, then bridge `X` will copy the frame only to the port connected to bridge `Y`, which will copy the frame only to the port connected to host `C`’s segment.

To simplify our pictures of LANs, we will draw the hubs and bridges and the wires that connect them as a single horizontal line, as shown in Figure 11.5.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659054942013.png" style="zoom: 40%" />

At a higher level in the hierarchy, multiple incompatible LANs can be connected by specialized computers called routers to form an internet (interconnected network). Each router has an adapter (port) for each network that it is connected to. Routers can also connect high-speed point-to-point phone connections, which are examples of networks known as WANs (wide area networks), so called because they span larger geographical areas than LANs. In general, routers can be used to build internets from arbitrary collections of LANs and WANs. For example, Figure 11.6 shows an example internet with a pair of LANs and WANs connected by three routers.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659054964256.png" style="zoom: 50%" />

The crucial property of an internet is that it can consist of different LANs and WANs with radically different and incompatible technologies. Each host is physically connected to every other host, but how is it possible for some source host to send data bits to another destination host across all of these incompatible networks?

The solution is a layer of protocol software running on each host and router that smoothes out the differences between the different networks. This software implements a protocol that governs how hosts and routers cooperate in order to transfer data. The protocol must provide two basic capabilities:

- Naming scheme. Different LAN technologies have different and incompatible ways of assigning addresses to hosts. The internet protocol smoothes these differences by defining a uniform format for host addresses. Each host is then assigned at least one of these internet addresses that uniquely identifies it.
- Delivery mechanism. Different networking technologies have different and incompatible ways of encoding bits on wires and of packaging these bits into frames. The internet protocol smoothes these differences by defining a uniform way to bundle up data bits into discrete chunks called packets. A packet consists of a header, which contains the packet size and addresses of the source and destination hosts, and a payload, which contains data bits sent from the source host.

Figure 11.7 shows an example of how hosts and routers use the internet protocol to transfer data across incompatible LANs. The example internet consists of two LANs connected by a router. A client running on host A, which is attached to LAN1, sends a sequence of data bytes to a server running on host B, which is attached to LAN2. 

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659055059108.png" style="zoom: 50%" />

There are eight basic steps:

1.  The client on host A invokes a system call that copies the data from the client’s virtual address space into a kernel buffer.
2.  The protocol software on host A creates a LAN1 frame by appending an internet header and a LAN1 frame header to the data. The internet header is addressed to internet host B. The LAN1 frame header is addressed to the router. It then passes the frame to the adapter. Notice that the payload of the LAN1 frame is an internet packet, whose payload is the actual user data. This kind of encapsulation is one of the fundamental insights of internetworking.
3. The LAN1 adapter copies the frame to the network.
4. When the frame reaches the router, the router’s LAN1 adapter reads it from the wire and passes it to the protocol software.
5. The router fetches the destination internet address from the internet packet header and uses this as an index into a routing table to determine where to forward the packet, which in this case is LAN2. The router then strips off the old LAN1 frame header, prepends a new LAN2 frame header addressed to host B, and passes the resulting frame to the adapter.
6. The router’s LAN2 adapter copies the frame to the network.
7. When the frame reaches host B, its adapter reads the frame from the wire and passes it to the protocol software.
8. Finally, the protocols of tware on host B strips off the packet header and frame header. The protocol software will eventually copy the resulting data into the server’s virtual address space when the server invokes a system call that reads the data.

Of course, we are glossing over many difficult issues here. What if different networks have different maximum frame sizes? How do routers know where to forward frames? How are routers informed when the network topology changes? What if a packet gets lost? Nonetheless, our example captures the essence of the internet idea, and encapsulation is the key.

## 11.3 The Global IP Internet

The global IP Internet is the most famous and successful implementation of an internet. It has existed in one form or another since 1969. While the internal architecture of the Internet is complex and constantly changing, the organization of client-server applications has remained remarkably stable since the early 1980s. Figure 11.8 shows the basic hardware and software organization of an Internet client-server application.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659055320357.png" style="zoom: 50%" />

Each Internet host runs software that implements the TCP/IP protocol (Transmission Control Protocol/Internet Protocol), which is supported by almost every modern computer system. Internet clients and servers communicate using a mix of sockets interface functions and Unix I/O functions. (We will describe the sockets interface in Section 11.4.) The sockets functions are typically implemented as system calls that trap into the kernel and call various kernel-mode functions in TCP/IP.

TCP/IP is actually a family of protocols, each of which contributes different capabilities. For example, IP provides the basic naming scheme and a delivery mechanism that can send packets, known as datagrams, from one Internet host to any other host. The IP mechanism is unreliable in the sense that it makes no effort to recover if datagrams are lost or duplicated in the network. UDP (Unreliable Datagram Protocol) extends IP slightly, so that datagrams can be transferred from process to process, rather than host to host. TCP is a complex protocol that builds on IP to provide reliable full duplex (bidirectional) connections between processes. To simplify our discussion, we will treat TCP/IP as a single monolithic protocol. We will not discuss its inner workings, and we will only discuss some of the basic capabilities that TCP and IP provide to application programs. We will not discuss UDP.

From a programmer’s perspective, we can think of the Internet as a worldwide collection of hosts with the following properties:

- The set of hosts is mapped to a set of 32-bit IP addresses.
- The set of IP addresses is mapped to a set of identifiers called Internet domain names.  
- A process on one Internet host can communicate with a process on any other Internet host over a connection.
 
The following sections discuss these fundamental Internet ideas in more detail.

### 11.3.1 IP Addresses

An IP address is an unsigned 32-bit integer. Network programs store IP addresses in the IP address structure shown in Figure 11.9.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659055383553.png" style="zoom: 50%" />

Storing a scalar address in a structure is an unfortunate artifact from the early implementations of the sockets interface. It would make more sense to define a scalar type for IP addresses, but it is too late to change now because of the enormous installed base of applications.

Because Internet hosts can have different host byte orders, TCP/IP defines a uniform network byte order (big-endian byte order) for any integer data item, such as an IP address, that is carried across the network in a packet header. <span class="comments red">Addresses in IP address structures are always stored in (big-endian) network byte order, even if the host byte order is little-endian.</span> Unix provides the following functions for converting between network and host byte order.

```c
#include <arpa/inet.h>

uint32_t htonl(uint32_t hostlong);
uint16_t htons(uint16_t hostshort);
Returns: value in network byte order

uint32_t ntohl(uint32_t netlong);
uint16_t ntohs(unit16_t netshort);
Returns: value in host byte order
```

The `htonl` function converts an unsigned 32-bit integer from host byte order to network byte order. The `ntohl` function converts an unsigned 32-bit integer from network byte order to host byte order. The `htons` and `ntohs` functions perform corresponding conversions for unsigned 16-bit integers. Note that there are no equivalent functions for manipulating 64-bit values.

IP addresses are typically presented to humans in a form known as dotted-decimal notation, where each byte is represented by its decimal value and separated from the other bytes by a period. For example, `128.2.194.242` is the dotted-decimal representation of the address `0x8002c2f2`. On Linux systems, you can use the hostname command to determine the dotted-decimal address of your own host:

```bash
linux> hostname -i 
128.2.210.175
```

Application programs can convert back and forth between IP addresses and dotted-decimal strings using the functions `inet_pton` and `inet_ntop`.

```c
#include <arpa/inet.h>

int inet_pton(AF_INET, const char *src, void *dst);
Returns: 1 if OK, 0 if src is invalid dotted decimal, −1 on error

const char *inet_ntop(AF_INET, const void *src, char *dst,
                      socklen_t size);
Returns: pointer to a dotted-decimal string if OK, NULL on error
```

In these function names, the `n` stands for network and the `p` stands for presentation. They can manipulate either 32-bit IPv4 addresses `(AF_INET)`, as shown here, or 128-bit IPv6 addresses `(AF_INET6)`, which we do not cover.

The `inet_pton` function converts a dotted-decimal string `(src)` to a binary IP address in network byte order `(dst)`. If `src` does not point to a valid dotted-decimal string, then it returns `0`. Any other error returns `−1` and sets `errno`. Similarly, the `inet_ntop` function converts a binary IP address in network byte order `(src)` to the corresponding dotted-decimal representation and copies at most size bytes of the resulting null-terminated string to `dst`.

### 11.3.2 Internet Domain Names

Internet clients and servers use IP addresses when they communicate with each other. However, large integers are difficult for people to remember, so the Internet also defines a separate set of more human-friendly domain names, as well as a mechanism that maps the set of domain names to the set of IP addresses. A domain name is a sequence of words (letters, numbers, and dashes) separated by periods, such as whaleshark.ics.cs.cmu.edu.

The set of domain names forms a hierarchy, and each domain name encodes its position in the hierarchy. An example is the easiest way to understand this. Figure 11.10 shows a portion of the domain name hierarchy.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659055513697.png" style="zoom: 50%" />


The hierarchy is represented as a tree. The nodes of the tree represent domain names that are formed by the path back to the root. Subtrees are referred to as subdomains. The first level in the hierarchy is an unnamed root node. The next level is a collection of first-level domain names that are defined by a nonprofit organization called *ICANN* (Internet Corporation for Assigned Names and Numbers). Common first-level domains include com, edu, gov, org, and net.

At the next level are second-level domain names such as `cmu.edu`, which are assigned on a first-come first-serve basis by various authorized agents of ICANN. Once an organization has received a second-level domain name, then it is free to create any other new domain name within its subdomain, such as `cs.cmu.edu`.

The Internet defines a mapping between the set of domain names and the set of IP addresses. Until 1988, this mapping was maintained manually in a single text file called `HOSTS.TXT`. Since then, the mapping has been maintained in a distributed worldwide database known as DNS (Domain Name System). Conceptually, the DNS database consists of millions of host entries, each of which defines the mapping between a set of domain names and a set of IP addresses. In a mathematical sense, think of each host entry as an equivalence class of domain names and IP addresses. We can explore some of the properties of the DNS mappings with the Linux `nslookup` program, which displays the IP addresses associated with a domain name.

Each Internet host has the locally defined domain name `localhost`, which always maps to the loopback address `127.0.0.1`:

```bash
linux> nslookup localhost 
Address: 127.0.0.1
```

The `localhost` name provides a convenient and portable way to reference clients and servers that are running on the same machine, which can be especially useful for debugging. We can use hostname to determine the real domain name of our local host:

```bash
linux> hostname 
whaleshark.ics.cs.cmu.edu
```

In the simplest case, there is a one-to-one mapping between a domain name and an IP address:

```bash
linux> nslookup whaleshark.ics.cs.cmu.edu 
Address: 128.2.210.175
```

However, in some cases, multiple domain names are mapped to the same IP address:

```bash
linux> nslookup cs.mit.edu 
Address: 18.62.1.6

linux> nslookup eecs.mit.edu 
Address: 18.62.1.6
```

In the most general case, multiple domain names are mapped to the same set of multiple IP addresses:

```bash
linux> nslookup www.twitter.com 
Address: 199.16.156.6  
Address: 199.16.156.70  
Address: 199.16.156.102 
Address: 199.16.156.230

linux> nslookup twitter.com 
Address: 199.16.156.102 
Address: 199.16.156.230 
Address: 199.16.156.6 
Address: 199.16.156.70
```

Finally, we notice that some valid domain names are not mapped to any IP address:

```bash
linux> nslookup edu  
*** Can’t find edu: No answer  
linux> nslookup ics.cs.cmu.edu  
*** Can’t find ics.cs.cmu.edu: No answer
```

### 11.3.3 Internet Connections

Internet clients and servers communicate by sending and receiving streams of bytes over connections. A connection is point-to-point in the sense that it connects a pair of processes. It is full duplex in the sense that data can flow in both directions at the same time. And it is reliable in the sense that—barring some catastrophic failure such as a cable cut by the proverbial careless backhoe operator—the stream of bytes sent by the source process is eventually received by the destination process in the same order it was sent.

A socket is an end point of a connection. Each socket has a corresponding socket address that consists of an Internet address and a 16-bit integer port and is denoted by the notation address:port.

```antd
const { Popover } = antd

const Paragraph = () => {
	return (
		<p>
			The port in the client’s socket address is assigned automatically by the kernel when the client makes a connection request and is known as an <Popover content='临时端口'><span className="comments">ephemeral port.</span></Popover> However, the port in the server’s socket address is typically some well-known port that is permanently associated with the service. For example, Web servers typically use port <code>80</code>, and email servers use port <code>25</code>. Associated with each service with a well-known port is a corresponding well-known service name. For example, the well-known name for the Web service is <code>http</code>, and the well-known name for email is <code>smtp</code>. The mapping between well-known names and well-known ports is contained in a file called <code>/etc/services</code>.
		</p>
	)
}

const root = ReactDOM.createRoot(el)
root.render(<Paragraph />)
```



A connection is uniquely identified by the socket addresses of its two end points. This pair of socket addresses is known as a socket pair and is denoted by the tuple

```c
(cliaddr:cliport, servaddr:servport)
```

where `cliaddr` is the client’s IP address, `cliport` is the client’s port, `servaddr` is the server’s IP address, and `servport` is the server’s port. For example, Figure 11.11 shows a connection between a Web client and a Web server.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659055736871.png" style="zoom: 50%" />


In this example, the Web client’s socket address is

```c
128.2.194.242:51213
```

where port `51213` is an ephemeral port assigned by the kernel. The Web server’s socket address is

```c
208.216.181.15:80
```

where port `80` is the well-known port associated with Web services. Given these client and server socket addresses, the connection between the client and server is uniquely identified by the socket pair

```c
(128.2.194.242:51213, 208.216.181.15:80)
```

## 11.4 The Sockets Interface

The sockets interface is a set of functions that are used in conjunction with the Unix I/O functions to build network applications. It has been implemented on most modern systems, including all Unix variants as well as Windows and Macintosh systems. Figure 11.12 gives an overview of the sockets interface in the context of a typical client-server transaction. You should use this picture as a road map when we discuss the individual functions.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058251305.png" style="zoom: 50%" />

### 11.4.1 Socket Address Structures

From the perspective of the Linux kernel, a socket is an end point for communication. From the perspective of a Linux program, a socket is an open file with a corresponding descriptor.

Internet socket addresses are stored in 16-byte structures having the type `sockaddr_in`, shown in Figure 11.13. For Internet applications, the `sin_family` field is `AF_INET`, the `sin_port` field is a 16-bit port number, and the `sin_addr` field contains a 32-bit IP address. The IP address and port number are always stored in network (big-endian) byte order.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058291990.png" style="zoom: 50%" />


The `connect`, `bind`, and `accept` functions require a pointer to a protocol-specific socket address structure. The problem faced by the designers of the sockets interface was how to define these functions to accept any kind of socket address structure. Today, we would use the generic `void *` pointer, which did not exist in C at that time. Their solution was to define `sockets` functions to expect a pointer to a generic `sockaddr` structure (Figure 11.13) and then require applications to cast any pointers to protocol-specific structures to this generic structure. To simplify our code examples, we follow Stevens’s lead and define the following type:

```c
typedef struct sockaddr SA;
```

We then use this type whenever we need to cast a `sockaddr_in` structure to a generic sockaddr structure.

### 11.4.2 The socket Function 

Clients and servers use the `socket` function to create a socket descriptor.

```c
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol);
Returns: nonnegative descriptor if OK, −1 on error
```

If we wanted the socket to be the end point for a connection, then we could call `socket` with the following hardcoded arguments:

```c
clientfd = Socket(AF_INET, SOCK_STREAM, 0);
```

where `AF_INET` indicates that we are using 32-bit IP addresses and `SOCK_ STREAM` indicates that the socket will be an end point for a connection. However, the best practice is to use the `getaddrinfo` function (Section 11.4.7) to generate these parameters automatically, so that the code is protocol-independent. We will show you how to use `getaddrinfo` with the `socket` function in Section 11.4.8.

The `clientfd` descriptor returned by socket is only partially opened and cannot yet be used for reading and writing. How we finish opening the socket depends on whether we are a client or a server. The next section describes how we finish opening the socket if we are a client.

### 11.4.3 The connect Function  

A client establishes a connection with a server by calling the `connect` function.

```c
#include <sys/socket.h>
int connect(int clientfd, const struct sockaddr *addr,
            socklen_t addrlen);
Returns: 0 if OK, −1 on error
```

The `connect` function attempts to establish an Internet connection with the server at socket address `addr`, where `addrlen` is `sizeof(sockaddr_in)`. The `connect` function blocks until either the connection is successfully established or an error occurs. If successful, the `clientfd` descriptor is now ready for reading and writing, and the resulting connection is characterized by the socket pair

```c
(x:y, addr.sin_addr:addr.sin_port)
```

where `x` is the client’s IP address and `y` is the ephemeral port that uniquely identifies the client process on the client host. As with `socket`, the best practice is to use `getaddrinfo` to supply the arguments to `connect` (see Section 11.4.8).

### 11.4.4 The bind Function

The remaining sockets functions—`bind`, `listen`, and `accept`—are used by servers to establish connections with clients.

```c
#include <sys/socket.h>
int bind(int sockfd, const struct sockaddr *addr,
         socklen_t addrlen);
Returns: 0 if OK, −1 on error
```

The `bind` function asks the kernel to associate the server’s socket address in `addr` with the socket descriptor `sockfd`. The `addrlen` argument is `sizeof(sockaddr_in)`. As with socket and connect, the best practice is to use `getaddrinfo` to supply the arguments to `bind` (see Section 11.4.8).

### 11.4.5 The listen Function

Clients are active entities that initiate connection requests. Servers are passive entities that wait for connection requests from clients. By default, the kernel assumes that a descriptor created by the socket function corresponds to an active socket that will live on the client end of a connection. <span class="comments green">A server calls the `listen` function to tell the kernel that the descriptor will be used by a server instead of a client.</span>

```c
#include <sys/socket.h>
int listen(int sockfd, int backlog);
Returns: 0 if OK, −1 on error
```

The `listen` function converts `sockfd` from an active socket to a listening socket that can accept connection requests from clients. The `backlog` argument is a hint about the number of outstanding connection requests that the kernel should queue up before it starts to refuse requests. The exact meaning of the `backlog` argument requires an understanding of TCP/IP that is beyond our scope. We will typically set it to a large value, such as `1,024`.

### 11.4.6 The accept Function

Servers wait for connection requests from clients by calling the `accept` function.

```c
#include <sys/socket.h>
int accept(int listenfd, struct sockaddr *addr, int *addrlen);
Returns: nonnegative connected descriptor if OK, −1 on error
```

The `accept` function waits for a connection request from a client to arrive on the listening descriptor `listenfd`, then fills in the client’s socket address in `addr`, and returns a connected descriptor that can be used to communicate with the client using Unix I/O functions.

The distinction between a listening descriptor and a connected descriptor confuses many students. <span class="comments green">The listening descriptor serves as an end point for client connection requests. It is typically created once and exists for the lifetime of the server. The connected descriptor is the end point of the connection that is established between the client and the server. It is created each time the server accepts a connection request and exists only as long as it takes the server to service a client.</span>

Figure 11.14 outlines the roles of the listening and connected descriptors. In step 1, the server calls `accept`, which waits for a connection request to arrive on the listening descriptor, which for concreteness we will assume is descriptor 3. Recall that descriptors 0–2 are reserved for the standard files.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058515601.png" style="zoom: 50%" />

In step 2, the client calls the `connect` function, which sends a connection request to `listenfd`. In step 3, the `accept` function opens a new connected descriptor `connfd` (which we will assume is descriptor 4), establishes the connection between `clientfd` and `connfd`, and then returns `connfd` to the application. The client also returns from the `connect`, and from this point, the client and server can pass data back and forth by reading and writing `clientfd` and `connfd`, respectively.

### 11.4.7 Host and Service Conversion

Linux provides some powerful functions, called `getaddrinfo` and `getnameinfo`, for converting back and forth between binary socket address structures and the string representations of hostnames, host addresses, service names, and port numbers. When used in conjunction with the sockets interface, they allow us to write network programs that are independent of any particular version of the IP protocol.

#### The getaddrinfo Function

The `getaddrinfo` function converts string representations of hostnames, host addresses, service names, and port numbers into socket address structures. It is the modern replacement for the obsolete `gethostbyname` and `getservbyname` functions. Unlike these functions, it is reentrant (see Section 12.7.2) and works with any protocol.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058632325.png" style="zoom: 50%" />

Given host and service (the two components of a socket address), `getaddrinfo` returns a result that points to a linked list of `addrinfo` structures, each of which points to a socket address structure that corresponds to host and service (Figure 11.15).


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058668647.png" style="zoom: 50%" />


After a client calls `getaddrinfo`, it walks this list, trying each socket address in turn until the calls to `socket` and `connect` succeed and the connection is established. Similarly, a server tries each socket address on the list until the calls to `socket` and `bind` succeed and the descriptor is bound to a valid socket address. To avoid memory leaks, the application must eventually free the list by calling `freeaddrinfo`. If `getaddrinfo` returns a nonzero error code, the application can call `gai_strerror` to convert the code to a message string.

The `host` argument to `getaddrinfo` can be either a domain name or a numeric address (e.g., a dotted-decimal IP address). The `service` argument can be either a service name (e.g., http) or a decimal port number. If we are not interested in converting the hostname to an address, we can set `host` to `NULL`. The same holds for `service`. However, at least one of them must be specified.

The optional hints argument is an `addrinfo` structure (Figure 11.16) that provides finer control over the list of socket addresses that `getaddrinfo` returns. When passed as a `hints` argument, only the `ai_family`, `ai_socktype`, `ai_protocol`, and `ai_flags` fields can be set. The other fields must be set to zero (or `NULL`). In practice, we use `memset` to zero the entire structure and then set a few selected fields:

- By default, `getaddrinfo` can return both IPv4 and IPv6 socket addresses. Setting `ai_family` to `AF_INET` restricts the list to IPv4 addresses. Setting it to `AF_INET6` restricts the list to IPv6 addresses.
- By default, for each unique address associated with host, the `getaddrinfo` function can return up to three `addrinfo` structures, each with a different `ai_socktype` field: one for connections, one for datagrams (not covered), and one for raw sockets (not covered). Setting `ai_socktype` to `SOCK_STREAM` restricts the list to at most one `addrinfo` structure for each unique address, one whose socket address can be used as the end point of a connection. This is the desired behavior for all of our example programs.
-  The `ai_flags` field is a bit mask that further modifies the default behavior. You create it by oring combinations of various values. Here are some that we find useful:
	- `AI_ADDRCONFIG`. This flag is recommended if you are using connections. It asks `getaddrinfo` to return IPv4 addresses only if the local host is configured for IPv4. Similarly for IPv6.
	- `AI_CANONNAME`. By default, the `ai_canonname` field is `NULL`. If this flag is set, it instructs `getaddrinfo` to point the `ai_canonname` field in the first `addrinfo` structure in the list to the canonical (official) name of host (see Figure 11.15).
	- `AI_NUMERICSERV`. By default, the service argument can be a service name or a port number. This flag forces the service argument to be a port number.
	- `AI_PASSIVE`. By default, `getaddrinfo` returns socket addresses that can be used by clients as active sockets in calls to connect. This flag instructs it to return socket addresses that can be used by servers as listening sockets. In this case, the host argument should be `NULL`. The address field in the resulting socket address structure(s) will be the wildcard address, which tells the kernel that this server will accept requests to any of the IP addresses for this host. This is the desired behavior for all of our example servers.

When `getaddrinfo` creates an `addrinfo` structure in the output list, it fills in each field except for `ai_flags`. The `ai_addr` field points to a socket address structure, the `ai_addrlen` field gives the size of this socket address structure, and the `ai_next` field points to the next `addrinfo` structure in the list. The other fields describe various attributes of the socket address.

One of the elegant aspects of `getaddrinfo` is that the fields in an `addrinfo` structure are opaque, in the sense that they can be passed directly to the functions in the sockets interface without any further manipulation by the application code. For example, `ai_family`, `ai_socktype`, and `ai_protocol` can be passed directly to socket. Similarly, `ai_addr` and `ai_addrlen` can be passed directly to `connect` and `bind`. This powerful property allows us to write clients and servers that are independent of any particular version of the IP protocol.

#### The getnameinfo Function

The `getnameinfo` function is the inverse of `getaddrinfo`. It converts a socket address structure to the corresponding host and service name strings. It is the modern replacement for the obsolete `gethostbyaddr` and `getservbyport` functions, and unlike those functions, it is reentrant and protocol-independent.

```c
#include <sys/socket.h>
#include <netdb.h>
int getnameinfo(const struct sockaddr *sa, socklen_t salen,
                char *host, size_t hostlen,
                char *service, size_t servlen, int flags);
Returns: 0 if OK, nonzero error code on error
```

The `sa` argument points to a socket address structure of size `salen` bytes, host to a buffer of size `hostlen` bytes, and service to a buffer of size `servlen` bytes. The `getnameinfo` function converts the socket address structure `sa` to the corresponding host and service name strings and copies them to the host and service buffers. If `getnameinfo` returns a nonzero error code, the application can convert it to a string by calling `gai_strerror`.

If we don’t want the hostname, we can set host to `NULL` and `hostlen` to zero. The same holds for the service fields. However, one or the other must be set.

The `flags` argument is a bit mask that modifies the default behavior. You create it by oring combinations of various values. Here are a couple of useful ones:

- `NI_NUMERICHOST`. By default, `getnameinfo` tries to return a domain name in host. Setting this flag will cause it to return a numeric address string instead.
- `NI_NUMERICSERV`. By default, getnameinfo will look in `/etc/services` and if possible, return a service name instead of a port number. Setting this flag forces it to skip the lookup and simply return the port number.

Figure 11.17 shows a simple program, called `hostinfo`, that uses `getaddrinfo` and `getnameinfo` to display the mapping of a domain name to its associated IP addresses. It is similar to the `nslookup` program from Section 11.3.2.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058838567.png" style="zoom: 50%" />

First, we initialize the `hints` structure so that `getaddrinfo` returns the addresses we want. In this case, we are looking for 32-bit IP addresses (line 16) that can be used as end points of connections (line 17). Since we are only asking `getaddrinfo` to convert domain names, we call it with a `NULL` service argument. 

After the call to `getaddrinfo`, we walk the list of `addrinfo` structures, using `getnameinfo` to convert each socket address to a dotted-decimal address string. After walking the list, we are careful to free it by calling `freeaddrinfo` (although for this simple program it is not strictly necessary).  

When we run `hostinfo`, we see that `twitter.com` maps to four IP addresses, which is what we saw using `nslookup` in Section 11.3.2.

```bash
linux> ./hostinfo twitter.com 
199.16.156.102  
199.16.156.230  
199.16.156.6
199.16.156.70
```

### 11.4.8 Helper Functions for the Sockets Interface

The `getaddrinfo` function and the sockets interface can seem somewhat daunting when you first learn about them. We find it convenient to wrap them with higher-level helper functions, called `open_clientfd` and `open_listenfd`, that clients and servers can use when they want to communicate with each other.

#### The open_clientfd Function  

A client establishes a connection with a server by calling `open_clientfd`.

```c
#include "csapp.h"
int open_clientfd(char *hostname, char *port);
Returns: descriptor if OK, −1 on error
```

The `open_clientfd` function establishes a connection with a server running on host hostname and listening for connection requests on port number port. It returns an open socket descriptor that is ready for input and output using the Unix I/O functions. Figure 11.18 shows the code for `open_clientfd`.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659058989355.png" style="zoom: 50%" />


We call `getaddrinfo`, which returns a list of `addrinfo` structures, each of which points to a socket address structure that is suitable for establishing a connection with a server running on hostname and listening on port. We then walk the list, trying each list entry in turn, until the calls to socket and connect succeed. If the connect fails, we are careful to close the socket descriptor before trying the next entry. If the connect succeeds, we free the list memory and return the socket descriptor to the client, which can immediately begin using Unix I/O to communicate with the server.

Notice how there is no dependence on any particular version of IP anywhere in the code. The arguments to socket and connect are generated for us automatically by `getaddrinfo`, which allows our code to be clean and portable.

#### The open_listenfd Function  

A server creates a listening descriptor that is ready to receive connection requests by calling the `open_listenfd` function.

```c
#include "csapp.h"
int open_listenfd(char *port);

Returns: descriptor if OK, −1 on error
```

The `open_listenfd` function returns a listening descriptor that is ready to receive connection requests on port port. Figure 11.19 shows the code for `open_listenfd`. The style is similar to `open_clientfd`. We call `getaddrinfo` and then walk the resulting list until the calls to `socket` and `bind` succeed. Note that in line 20 we use the `setsockopt` function (not described here) to configure the server so that it can be terminated, be restarted, and begin accepting connection requests immediately. By default, a restarted server will deny connection requests from clients for approximately 30 seconds, which seriously hinders debugging.  

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/image-for-2022@master/iShot_2022-07-29_09.44.39.png" style="zoom: 50%" />

Since we have called `getaddrinfo` with the `AI_PASSIVE` flag and a `NULL` host argument, the address field in each socket address structure is set to the wildcard address, which tells the kernel that this server will accept requests to any of the IP addresses for this host. 

Finally, we call the `listen` function to convert `listenfd` to a listening descriptor and return it to the caller. If the listen fails, we are careful to avoid a memory leak by closing the descriptor before returning.

### 11.4.9 Example Echo Client and Server

The best way to learn the sockets interface is to study example code. Figure 11.20 shows the code for an echo client. After establishing a connection with the server, the client enters a loop that repeatedly reads a text line from standard input, sends the text line to the server, reads the echo line from the server, and prints the result to standard output. The loop terminates when `fgets` encounters `EOF` on standard input, either because the user typed <kbd>Ctrl+D</kbd> at the keyboard or because it has exhausted the text lines in a redirected input file.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059167747.png" style="zoom: 50%" />


After the loop terminates, the client closes the descriptor. This results in an `EOF` notification being sent to the server, which it detects when it receives a return code of zero from its `rio_readlineb` function. After closing its descriptor, the client terminates. Since the client’s kernel automatically closes all open descriptors when a process terminates, the close in line 24 is not necessary. However, it is good programming practice to explicitly close any descriptors that you have opened.

Figure 11.21 shows the main routine for the echo server. After opening the listening descriptor, it enters an infinite loop. Each iteration waits for a connection request from a client, prints the domain name and port of the connected client, and then calls the echo function that services the client. After the echo routine returns, the main routine closes the connected descriptor. Once the client and server have closed their respective descriptors, the connection is terminated.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059200080.png" style="zoom: 50%" />


The clientaddr variable in line 9 is a socket address structure that is passed to accept. Before accept returns, it fills in clientaddr with the socket address of the client on the other end of the connection. Notice how we declare clientaddr as type struct `sockaddr_storage` rather than struct `sockaddr_in`. By definition, the `sockaddr_storage` structure is large enough to hold any type of socket address, which keeps the code protocol-independent.

Notice that our simple echo server can only handle one client at a time. A server of this type that iterates through clients, one at a time, is called an iterative server. In Chapter 12, we will learn how to build more sophisticated concurrent servers that can handle multiple clients simultaneously.

Finally, Figure 11.22 shows the code for the echo routine, which repeatedly reads and writes lines of text until the `rio_readlineb` function encounters `EOF` in line 10.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059226562.png" style="zoom: 50%" />

## 11.5 Web Servers

So far we have discussed network programming in the context of a simple echo server. In this section, we will show you how to use the basic ideas of network programming to build your own small, but quite functional, Web server.

### 11.5.1 Web Basics

Web clients and servers interact using a text-based application-level protocol known as HTTP (hypertext transfer protocol). HTTP is a simple protocol. A Web client (known as a browser) opens an Internet connection to a server and requests some content. The server responds with the requested content and then closes the connection. The browser reads the content and displays it on the screen.

What distinguishes Web services from conventional file retrieval services such as FTP? The main difference is that Web content can be written in a language known as HTML (hypertext markup language). An HTML program (page) contains instructions (tags) that tell the browser how to display various text and graphical objects in the page. For example, the code

```html
<b> Make me bold! </b>
```

tells the browser to print the text between the `<b>` and `</b>` tags in boldface type. However, the real power of HTML is that a page can contain pointers (hyperlinks) to content stored on any Internet host. For example, an HTML line of the form

```html
<a href="http://www.cmu.edu/index.html">Carnegie Mellon</a>
```

tells the browser to highlight the text object Carnegie Mellon and to create a hyperlink to an HTML file called index.html that is stored on the CMU Web server. If the user clicks on the highlighted text object, the browser requests the corresponding HTML file from the CMU server and displays it.


### 11.5.2 Web Content

To Web clients and servers, content is a sequence of bytes with an associated MIME (multipurpose internet mail extensions) type. Figure 11.23 shows some common MIME types.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059369528.png" style="zoom: 50%" />


Web servers provide content to clients in two different ways:

-  Fetch a disk file and return its contents to the client. The disk file is known as static content and the process of returning the file to the client is known as serving static content.
- Run an executable file and return its output to the client. The output produced by the executable at run time is known as dynamic content, and the process of running the program and returning its output to the client is known as serving dynamic content.

Every piece of content returned by a Web server is associated with some file that it manages. Each of these files has a unique name known as a URL (universal resource locator). For example, the URL

```html
http://www.google.com:80/index.html
```

identifies an HTML file called `/index.html` on Internet host `www.google.com` that is managed by a Web server listening on port 80. The port number is optional and defaults to the well-known HTTP port 80. URLs for executable files can include program arguments after the filename. A `?` character separates the filename from the arguments, and each argument is separated by an `&` character. For example, the URL

```http
http://bluefish.ics.cs.cmu.edu:8000/cgi-bin/adder?15000&213
```

identifies an executable called `/cgi-bin/adder` that will be called with two argument strings: `15000` and `213`. Clients and servers use different parts of the URL during a transaction. For instance, a client uses the prefix

```http
http://www.google.com:80
```

to determine what kind of server to contact, where the server is, and what port it is listening on. The server uses the suffix

```html
/index.html
```

to find the file on its filesystem and to determine whether the request is for static or dynamic content.

There are several points to understand about how servers interpret the suffix of a URL:

- There are no standard rules for determining whether a URL refers to static or dynamic content. Each server has its own rules for the files it manages. A classic (old-fashioned) approach is to identify a set of directories, such as cgi-bin, where all executables must reside.
    
- The initial `/` in the suffix does not denote the Linux root directory. Rather, it denotes the home directory for whatever kind of content is being requested. For example, a server might be configured so that all static content is stored in directory `/usr/httpd/html` and all dynamic content is stored in directory `/usr/httpd/cgi-bin`.
    
- The minimal URL suffix is the `/` character, which all servers expand to some default home page such as `/index.html`. This explains why it is possible to fetch the home page of a site by simply typing a domain name to the browser. The browser appends the missing `/` to the URL and passes it to the server, which expands the `/` to some default filename.

### 11.5.3 HTTP Transactions

Since HTTP is based on text lines transmitted over Internet connections, we can use the Linux `telnet` program to conduct transactions with any Web server on the Internet. The `telnet` program has been largely supplanted by `ssh` as a remote login tool, but it is very handy for debugging servers that talk to clients with text lines over connections. For example, Figure 11.24 uses telnet to request the home page from the AOL Web server.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059516571.png" style="zoom: 50%" />


In line 1, we run `telnet` from a Linux shell and ask it to open a connection to the AOL Web server. Telnet prints three lines of output to the terminal, opens the connection, and then waits for us to enter text (line 5). Each time we enter a text line and hit the enter key, telnet reads the line, appends carriage return and line feed characters (`\r\n` in C notation), and sends the line to the server. This is consistent with the HTTP standard, which requires every text line to be terminated by a carriage return and line feed pair. To initiate the transaction, we enter an HTTP request (lines 5–7). The server replies with an HTTP response (lines 8–17) and then closes the connection (line 18).

#### HTTP Requests

An HTTP request consists of a request line (line 5), followed by zero or more request headers (line 6), followed by an empty text line that terminates the list of headers (line 7). A request line has the form 

```http
method URI version
```

HTTP supports a number of different methods, including `GET`, `POST`, `OPTIONS`, `HEAD`, `PUT`, `DELETE`, and `TRACE`. We will only discuss the workhorse `GET` method, which accounts for a majority of HTTP requests. The `GET` method instructs the server to generate and return the content identified by the URI (uniform resource identifier). The URI is the suffix of the corresponding URL that includes the filename and optional arguments.3

The version field in the request line indicates the HTTP version to which the request conforms. The most recent HTTP version is HTTP/1.1. HTTP/1.0 is an earlier, much simpler version from 1996. HTTP/1.1 defines additional headers that provide support for advanced features such as caching and security, as well as a mechanism that allows a client and server to perform multiple transactions over the same persistent connection. In practice, the two versions are compatible because HTTP/1.0 clients and servers simply ignore unknown HTTP/1.1 headers.

To summarize, the request line in line 5 asks the server to fetch and return the HTML file `/index.html`. It also informs the server that the remainder of the request will be in HTTP/1.1 format.

Request headers provide additional information to the server, such as the brand name of the browser or the MIME types that the browser understands. Request headers have the form

```http
header-name: header-data
```

For our purposes, the only header to be concerned with is the Host header (line 6), which is required in HTTP/1.1 requests, but not in HTTP/1.0 requests. The Host header is used by proxy caches, which sometimes serve as intermediaries between a browser and the origin server that manages the requested file. Multiple proxies can exist between a client and an origin server in a so-called proxy chain. The data in the Host header, which identifies the domain name of the origin server, allow a proxy in the middle of a proxy chain to determine if it might have a locally cached copy of the requested content.

Continuing with our example in Figure 11.24, the empty text line in line 7 (generated by hitting enter on our keyboard) terminates the headers and instructs the server to send the requested HTML file.

#### HTTP Responses

HTTP responses are similar to HTTP requests. An HTTP response consists of a response line (line 8), followed by zero or more response headers (lines 9–13), followed by an empty line that terminates the headers (line 14), followed by the response body (lines 15–17). A response line has the form

```http
version status-code status-message
```

The version field describes the HTTP version that the response conforms to. The status-code is a three-digit positive integer that indicates the disposition of the request. The status-message gives the English equivalent of the error code. Figure 11.25 lists some common status codes and their corresponding messages.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059619859.png" style="zoom: 50%" />

The response headers in lines 9–13 provide additional information about the response. For our purposes, the two most important headers are Content-Type (line 12), which tells the client the MIME type of the content in the response body, and Content-Length (line 13), which indicates its size in bytes.

The empty text line in line 14 that terminates the response headers is followed by the response body, which contains the requested content.

### 11.5.4 Serving Dynamic Content

If we stop to think for a moment how a server might provide dynamic content to a client, certain questions arise. For example, how does the client pass any program arguments to the server? How does the server pass these arguments to the child process that it creates? How does the server pass other information to the child that it might need to generate the content? Where does the child send its output? These questions are addressed by a de facto standard called CGI (common gateway interface).

#### How Does the Client Pass Program Arguments to the Server?

Arguments for `GET` requests are passed in the URI. As we have seen, a `?` character separates the filename from the arguments, and each argument is separated by an `&` character. Spaces are not allowed in arguments and must be represented with the `%20` string. Similar encodings exist for other special characters.

#### How Does the Server Pass Arguments to the Child?

After a server receives a request such as

```http
GET /cgi-bin/adder?15000&213 HTTP/1.1
```

it calls `fork` to create a child process and calls `execve` to run the `/cgi-bin/adder` program in the context of the child. Programs like the `adder` program are often referred to as CGI programs because they obey the rules of the CGI standard. Before the call to execve, the child process sets the CGI environment variable `QUERY_STRING` to `15000&213`, which the `adder` program can reference at run time using the Linux `getenv` function.

#### How Does the Server Pass Other Information to the Child?

CGI defines a number of other environment variables that a CGI program can expect to be set when it runs. Figure 11.26 shows a subset.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059719270.png" style="zoom: 40%" />


#### Where Does the Child Send Its Output?

A CGI program sends its dynamic content to the standard output. Before the child process loads and runs the CGI program, it uses the Linux `dup2` function to redirect standard output to the connected descriptor that is associated with the client. Thus, anything that the CGI program writes to standard output goes directly to the client.

Notice that since the parent does not know the type or size of the content that the child generates, the child is responsible for generating the Content-type and Content-length response headers, as well as the empty line that terminates the headers.

Figure 11.27 shows a simple CGI program that sums its two arguments and returns an HTML file with the result to the client. 


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059747153.png" style="zoom: 50%" />


Figure 11.28 shows an HTTP transaction that serves dynamic content from the adder program.


<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059765981.png" style="zoom: 50%" />

## 11.6 Putting It Together: The Tiny Web Server

We conclude our discussion of network programming by developing a small but functioning Web server called Tiny. Tiny is an interesting program. It combines many of the ideas that we have learned about, such as process control, Unix I/O, the sockets interface, and HTTP, in only 250 lines of code. While it lacks the functionality, robustness, and security of a real server, it is powerful enough to serve both static and dynamic content to real Web browsers. We encourage you to study it and implement it yourself. It is quite exciting (even for the authors!) to point a real browser at your own server and watch it display a complicated Web page with text and graphics.

### The Tiny main Routine

Figure 11.29 shows Tiny’s main routine. Tiny is an iterative server that listens for connection requests on the port that is passed in the command line. After opening a listening socket by calling the `open_listenfd` function, Tiny executes the typical infinite server loop, repeatedly accepting a connection request (line 32), performing a transaction (line 36), and closing its end of the connection (line 37).


<img src="https://cdn.staticaly.com/gh/LastKnightCoder/image-for-2022@master/iShot_2022-07-29_09.57.15.png" style="zoom: 50%" />


### The doit Function

The `doit` function in Figure 11.30 handles one HTTP transaction. First, we read and parse the request line (lines 11–14). Notice that we are using the `rio_ readlineb` function from Figure 10.8 to read the request line.


<img src="https://cdn.staticaly.com/gh/LastKnightCoder/image-for-2022@master/iShot_2022-07-29_09.58.02.png" style="zoom: 50%" />


Tiny supports only the `GET` method. If the client requests another method (such as `POST`), we send it an error message and return to the main routine (lines 15–19), which then closes the connection and awaits the next connection request. Otherwise, we read and (as we shall see) ignore any request headers (line 20).

Next, we parse the URI into a filename and a possibly empty CGI argument string, and we set a flag that indicates whether the request is for static or dynamic content (line 23). If the file does not exist on disk, we immediately send an error message to the client and return.

Finally, if the request is for static content, we verify that the file is a regular file and that we have read permission (line 31). If so, we serve the static content (line 36) to the client. Similarly, if the request is for dynamic content, we verify that the file is executable (line 39), and, if so, we go ahead and serve the dynamic content (line 44).

### The clienterror Function

Tiny lacks many of the error-handling features of a real server. However, it does check for some obvious errors and reports them to the client. The `clienterror` function in Figure 11.31 sends an HTTP response to the client with the appropriate status code and status message in the response line, along with an HTML file in the response body that explains the error to the browser’s user.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659059950863.png" style="zoom: 50%" />

Recall that an HTML response should indicate the size and type of the content in the body. Thus, we have opted to build the HTML content as a single string so that we can easily determine its size. Also, notice that we are using the robust `rio_writen` function from Figure 10.4 for all output.

### The read_requesthdrs Function

Tiny does not use any of the information in the request headers. It simply reads and ignores them by calling the `read_requesthdrs` function in Figure 11.32. Notice that the empty text line that terminates the request headers consists of a carriage return and line feed pair, which we check for in line 6.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659060175634.png" style="zoom: 50%" />

### The parse_uri Function

Tiny assumes that the home directory for static content is its current directory and that the home directory for executables is `./cgi-bin`. Any URI that contains the string cgi-bin is assumed to denote a request for dynamic content. The default filename is `./home.html`.

The `parse_uri` function in Figure 11.33 implements these policies. It parses the URI into a filename and an optional CGI argument string. If the request is for static content (line 5), we clear the CGI argument string (line 6) and then convert the URI into a relative Linux pathname such as `./index.html` (lines 7–8). If the URI ends with a `/` character (line 9), then we append the default filename (line 10). On the other hand, if the request is for dynamic content (line 13), we extract any CGI arguments (lines 14–20) and convert the remaining portion of the URI to a relative Linux filename (lines 21–22).

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659060229368.png" style="zoom: 50%" />

### The serve_static Function

Tiny serves five common types of static content: HTML files, unformatted text files, and images encoded in GIF, PNG, and JPEG formats.

The `serve_static` function in Figure 11.34 sends an HTTP response whose body contains the contents of a local file. First, we determine the file type by inspecting the suffix in the filename (line 7) and then send the response line and response headers to the client (lines 8–13). Notice that a blank line terminates the headers.

<img src="https://cdn.staticaly.com/gh/LastKnightCoder/image-for-2022@master/iShot_2022-07-29_10.04.16.png" style="zoom: 50%" />

Next, we send the response body by copying the contents of the requested file to the connected descriptor `fd`. The code here is somewhat subtle and needs to be studied carefully. Line 18 opens filename for reading and gets its descriptor. In line 19, the Linux `mmap` function maps the requested file to a virtual memory area. Recall from our discussion of `mmap` in Section 9.8 that the call to `mmap` maps the first filesize bytes of file `srcfd` to a private read-only area of virtual memory that starts at address srcp.

Once we have mapped the file to memory, we no longer need its descriptor, so we close the file (line 20). Failing to do this would introduce a potentially fatal memory leak. Line 21 performs the actual transfer of the file to the client. The `rio_writen` function copies the filesize bytes starting at location `srcp` (which of course is mapped to the requested file) to the client’s connected descriptor. Finally, line 22 frees the mapped virtual memory area. This is important to avoid a potentially fatal memory leak.

### The serve_dynamic Function

Tiny serves any type of dynamic content by forking a child process and then running a CGI program in the context of the child.

The `serve_dynamic` function in Figure 11.35 begins by sending a response line indicating success to the client, along with an informational Server header. The CGI program is responsible for sending the rest of the response. Notice that this is not as robust as we might wish, since it doesn’t allow for the possibility that the CGI program might encounter some error.

<img src="https://cdn.jsdelivr.net/gh/LastKnightCoder/image-for-2022@master/1659060387048.png" style="zoom: 50%" />

After sending the first part of the response, we fork a new child process (line 11). The child initializes the `QUERY_STRING` environment variable with the CGI arguments from the request URI (line 13). Notice that a real server would set the other CGI environment variables here as well. For brevity, we have omitted this step.

Next, the child redirects the child’s standard output to the connected file descriptor (line 14) and then loads and runs the CGI program (line 15). Since the CGI program runs in the context of the child, it has access to the same open files and environment variables that existed before the call to the `execve` function. Thus, everything that the CGI program writes to standard output goes directly to the client process, without any intervention from the parent process. Meanwhile, the parent blocks in a call to wait, waiting to reap the child when it terminates (line 17).

## 11.7 Summary

Every network application is based on the client-server model. With this model, an application consists of a server and one or more clients. The server manages resources, providing a service for its clients by manipulating the resources in some way. The basic operation in the client-server model is a client-server transaction, which consists of a request from a client, followed by a response from the server.

Clients and servers communicate over a global network known as the Internet. From a programmer’s point of view, we can think of the Internet as a worldwide collection of hosts with the following properties: (1) Each Internet host has a unique 32-bit name called its IP address. (2) The set of IP addresses is mapped to a set of Internet domain names. (3) Processes on different Internet hosts can communicate with each other over connections.

Clients and servers establish connections by using the sockets interface. A socket is an end point of a connection that is presented to applications in the form of a file descriptor. The sockets interface provides functions for opening and closing socket descriptors. Clients and servers communicate with each other by reading and writing these descriptors.

Web servers and their clients (such as browsers) communicate with each other using the HTTP protocol. A browser requests either static or dynamic content from the server. A request for static content is served by fetching a file from the server’s disk and returning it to the client. A request for dynamic content is served by running a program in the context of a child process on the server and returning its output to the client. The CGI standard provides a set of rules that govern how the client passes program arguments to the server, how the server passes these arguments and other information to the child process, and how the child sends its output back to the client. A simple but functioning Web server that serves both static and dynamic content can be implemented in a few hundred lines of C code.


