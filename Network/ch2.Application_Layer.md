 # Applicaion Layer
 * 가장 유명한 protocol은 HTTP
 * 통신 기능이 있는 process가 있는 곳이므로, 반대편의 process만 신경 쓰면 된다.

## Client-server architecture
### server
* always-on host
* permanent IP address
* data centers for scaling

### clients
* communicate with server
* may be intermittently connected
* may have dynamic IP addresses
* do not communicate directly with each other
> **socket**: process가 통신할 수 있게 하는 interface  

> **IP address**: socket의 index

## Processes communicating
### process
: program running within a host
* within same host -> by inter-process communication (defined by OS)
* in different hosts -> by exchanging messages

## Sockets
* process sends/receives messages to/from its socket
* socket analogous to door 
![](https://i.imgur.com/s8MxsVV.png)

## What transport service does an app need?
* data integrity
    * data를 온전히 보내줬으면..
    * transport service는 이것만 보장해준다!
* timing
    * 얼마 동안의 시간 안에 전달해줬으면..
* throughput
    * 처리량이 어느정도 였으면..
* security

## Web & HTTP
* web page consists of **objects**
* object can be HTML file, JPEG, Java applet, audio file, ...
* web page consists of base HTML-file which includes several referenced objects
* each object is addressable by a URL

## HTTP overview
### uses TCP:
* client initiates TCP connection (creates socket) to server, port 80
* server accepts TCP connection from client
* HTTP messages are exchanged
* TCP connection closed
### HTTP is "stateless"

## HTTP connections
### non-persistent HTTP
* 한 object에 대해 TCP connection이 닫힘
### persistent HTTP
* 여러 object이 한 TCP connection에 대해 전송될 수 있음