# TCP Protocol Implementation


## Foreword
When I tried to implement TCP protocol, it's hard for me to find a whole teaching document on Google that tell me how to implement it in details. Most of them are talking about only part of the protocol, like congestion control, 3 way handshake, etc. So I can't form a whole picture of TCP. After going through a long and tough time I finally understand the protocol thoroughly, and here I want to introduce the protocol in a heulistic way.

## Definition

What is TCP? In short it is a trustworthy protocol program in Transport Layer, which can transmit the packet safely. Unlike UDP, you don't have to worry that there is loss of packet or wrong bits inside the packet. You can think TCP as a post office: you give the message to it, then it will safely transport the message to the receiver. 

## Whole Picture

TCP can be roughly divided into 3 stages. 

1. Building Connection
2. Send / receive message
3. Close Connection

Among them, send / receive message is the most tedious one.

To make it clear for the reader, I would inform you of the behavior of both receiver and sender in each of three stages.

## Build Connection (3 way handshake)

### Scenario

![handshake](img/3-way_handshake.png)

[To be continue]