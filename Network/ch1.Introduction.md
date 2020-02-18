# Introduction
## overview
* what's the Internet
* what's a protocol?
* network edge
* access net, physical media
* network core
* Internet/ISP structure
* performance: loss, delay
* protocol layers, service models
* network modeling

## network structure
1. network edge:  
applications and hosts
1. network core  
    * routers
    * network of networks
1. access networks, physical media:  
communication links

## The network edge
* end systems(hosts):  
    * run applicatio programs
    * e.g. Web, email
* client/server model
    * client host requests, receives service from always-on server
    * e.g. Web browser/server; email client/server
* peer-peer model:  
    * minimal use of dedicated servers
    * e.g. Skype, BitTorrent, KaZaA

## Network edge: connection-oriented service

### *Goal*: data transfer between end systems

* ***Connection***: prepare for data transfer ahead of time
    * Request / Respond
    * set up "state" in 2 communicating hosts
* **TCP** - Transmission Control Protocol
    * Internet's connection-oriented service
    * *reliable, in-order*(믿을 만하고, 순서를 지키는) byte-stream data transfer
        * loss: acknowledgements and retransmissions
    * *flow control*: sender won't overwhelm receiver(sender가 보내는 속도를 조절함)
    * *congestion control*: senders "slow down sending rate" when network congested

## What's a protocol?
* All communication in Internet **coordinated** by protocols

## The Network Core
* mesh of interconnected routers  
router가 얽히고 섥힌 집합
* how is data transferred through net?
    * circuit switching:  
    dedicated circuit per call: telephone net
    * packet-switching:  
    data sent thru net in discrete "chunks"
    
## Packet Switching: Statistical Multiplexing
![](https://i.imgur.com/VLr9Jat.png)

### packet switching versus circuit switching
Packet switching allows more users to use network!
* 1Mb/s link라고 하자
* circuit switching일 때 각 user 당 100kb/s라 하면 10명만 이용 가능
* packet switching일 땐 35명이 있을 때 10명이 동시에 active인 확률은 0.0004

## How do loss and delay occur?
packets queue in router buffers
* packet arrival rate to link exceeds output link capacity

## 4 sources of packet delay
1. nodal processing
    * check bit errors
    * determine output link
1. queueing
    * time waiting at output link for transmission
    * depends on congestion level of router (사람들이 몰리면 delay가 생김)
    * queue 크기보다 더 많은 packet이 들어오면? > packet loss 발생(loss의 90%)
1. Transmission delay
    * R = link bandwidth (bps)
    * L = packet length (bits)
    * time to send bits into link = L/R
1. Propagation delay
    * d = length of physical link
    * s = propagation speed in medium (~2*10^8 m/sec)
    * propagation delay = d/s
![](https://i.imgur.com/vHYrJzC.png)
