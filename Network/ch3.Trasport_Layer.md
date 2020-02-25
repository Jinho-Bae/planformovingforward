# Transport Layer
## Multiplexing / demultiplexing
* **demultiplexing at rcv host:**  
delivering received segments to correct socket
* **multiplexing at send host:**  
gathering data from multiple sockets, enveloping data with header
![](https://i.imgur.com/IlpQPUn.png)

## How demultiplexing works
* host receives IP datagrams
    * each datagram has source IP address, destination IP address
    * each datagram carries 1 transport layer segment
    * each segment has source, destination port number
    * port를 보고 [de]multiplexing을 한다.
![](https://i.imgur.com/cMPAddO.png)
* UDP의 경우, dest IP&port만 보고 demultiplexing함.
* TCP의 경우, src IP&port, dest IP&port 보고 demultiplexing
    * 넷 중 하나라도 다르면 다른 socket으로 감.

## UDP: User Datagram Protocol
* unreliable deliveries
* no order guarantees
* connectionless
    * no handshaking between UDP sender & receiver
    * each UDP segment handled independently of others
> Why is there a UDP?
> * no connection establishment (no delay)
> * no connection state
> * small segment header
> * no congestion control: UDP can blast away(날려버리다) as fast as desired(원하는 대로)

![](https://i.imgur.com/jZhzu3K.png)
* header의 field가 4개 뿐이라 단순
* often used for streaming multimedia apps
    * loss tolerant
    * rate sensitive
* other UDP uses
    * DNS
    * SNMP