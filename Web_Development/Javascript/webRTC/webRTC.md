## All about webRTC
WebRTC is an open framework that enables Real Time Communication(RTC), which is very useful for things like Video-Audio calling, Chatting, P2P file sharing, …

It is the open standard for real-time communication, which powers so many products and is being used in production at huge organizations. For example, Google Meet, Discord, Facebook Messenger, and many more are being powered by WebRTC. Applications like Zoom makes use of WebRTC indirectly for the data channels and stuff.

Let’s take a look at some terminologies and stuff related to WebRTC, while we learn about how it works when connecting two users for a video call.

# <>IP Addresses and NAT

We all know that every device must have a  **_unique_** IP address in order to access the internet. But what happens when the number of available IP addresses is less than the number of users wanting to use the internet? That’s exactly the case with  [IPv4 addresses](https://en.wikipedia.org/wiki/IPv4_address_exhaustion).

IPv6 has been carefully designed to avoid these problems in the future.
[NAT ](https://youtu.be/FTUV0t6JaDA)
But to solve this issue for IPv4, Network Address Translation (NAT) is used in the router, which uses its own set of IP addresses for the devices below it, also known as  _local/private_  IP addresses(10.x.x.x, 192.168.x.x, 172.16.0.0–172.31.255.255). Only the NAT and peers of the local network can communicate through this  _private_ address.

Thus, because of NAT, we would have two IP addresses; one being  **public,** associated with the  router, visible only from the outside and the other, being a  **private** address visible only to those connected to the same router.

# **</>**

It’s not possible to connect two peers directly with just their IP addresses. Most of the connections would require you to pass on a unique address to locate you on the internet, a relay server to pass on media, if your router doesn’t allow P2P connections and last but not least, you would have to bypass the firewall.

In order to facilitate this, a framework called  [Interactive Connectivity Establishment (ICE)](https://www.geeksforgeeks.org/interactive-connectivity-establishment-ice/)  is used to connect the end users.

# THE STEPS TO MAKE A CONNECTION

We need to pass on some information back and forth between the clients before a direct connection can be established. That’s exactly the reason why WebRTC is not fully  [P2P](https://www.investopedia.com/terms/p/peertopeer-p2p-service.asp).

In order to initialize a connection, a signaling server is required to transfer some essential information between the peers. Once the connection is established, we won’t require the signaling server thereafter.

Let’s look at the steps we need to do during signaling now.  ![](https://miro.medium.com/max/665/1*AElvgD3SVUkKvB-BH97gJA.png)

1.  The first user has to send an OFFER SDP to the other user.
2.  The second user has to accept that SDP and send an ANSWER SDP in return.
3.  Then, the first user should accept that SDP and after that either one of them has to send the ICE CANDIDATES generated, to the other.
4.  The latter has to accept the received candidates.

After doing all these, the connection will be established.

But now you are confused about what a SDP is, and what an ICE Candidate is, right? Don’t worry.

# <>Session Description Protocol(SDP)

[Session Description Protocol or simply SDP](https://tools.ietf.org/html/rfc4566), is a protocol for describing multimedia sessions. Normally, it contains description of multimedia content of the connection, such as resolution, formats, codecs, encryption, and others. This is transferred so that the other peer can know about what and how the media will be sent, before connection establishment.

So, transferring SDP means that you are telling the other user about what you are gonna be sending, after the direct connection(P2P) has been established.

# </>

Now, before we take a look at what an ICE Candidate is all about, let’s try to understand more about how the connection is made with the ICE framework.

Let’s try out some scenarios for a WebRTC video call.

**_Scenario 1: Both users connected over a local network:_**

If both users are on a local network, then there’s no problem in connecting each other. They can communicate directly with each other, with just their local/private IP address. But mostly, it’s an unlikely-to-happen scenario.

**_Scenario 2: Both users connected over the internet:_**

When both users are not under the local network, the connection becomes a little complicated. As the user can only see his  _private_ IP address due to NAT, a connection cannot be established from outside the local network.

In order to overcome this, we use a STUN server.

# <>**Session Traversal Utilities for NAT(STUN)**

The  [STUN server](https://help.singlecomm.com/hc/en-us/articles/115007993947-STUN-servers-A-Quick-Start-Guide)  allow clients to find out their  _public_ IP address and the type of NAT they are behind. This information is used to establish the media connection later on. In most cases, a STUN will be necessary to facilitate a connection.

By making contact with the STUN server before sending the SDP, one can connect with the other user, even if they are not on the local network.

# </>

**_Scenario 3: Both users connected over the internet but direct connection is not possible:_**

One may think that, using the STUN server would solve all the problems for the connections through internet. Knowing the  _public_ IP address is great, but it might not be enough for the connection to be established. There are cases where a direct connection is not possible at all.

> Around 80% of all connections can be resolved by either using the local IP address or by the use of STUN and public IP addresses.

Some of the cases might be; one of the peer is behind a  [Symmetric NAT](https://www.dialogic.com/webhelp/BorderNet2020/1.0.0/WebHelp/nat_traversal.htm)(a more secure type of NAT) or simply that the firewall doesn’t allow P2P connections to be made. In these situations, we need a server to relay the media between both the peers throughout the connection, unlike a STUN server, which is only needed once before the connection is established.

What we use here is called a TURN server.

# <>**Traversal Using Relay NAT**  (TURN)

A TURN server is used to bypass the Symmetric NAT restriction and relay all information/media through that server. The first peer has to create a connection to the TURN server and ask the other to do the same, so that all the information the other peer sends to the server is forwarded to the former.

> TURN servers usually come with their own STUN servers integrated, so an additional STUN server is not necessary. TURN servers are often referred as ‘Relay Servers’.

As you probably know, TURN servers usually are expensive to maintain due to its bandwidth usage. So, it should only be used in cases where no other means of connection is possible.

# </>

Now you may think that, there are so many things to look at, before making a connection. So, how is it possible to always use the efficient path to communicate?

This is where the ICE framework jumps in. WebRTC finds the most efficient network path for the connection to be established.

# <>ICE Candidate

Just like how SDP is used to exchange information about the media, peers must also exchange information about the network connection. This is what an  [ICE candidate](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidate)  is for. They contain all the details about the available methods to communicate, like a direct connection or through a TURN server.

ICE candidates are generated automatically by the WebRTC framework itself, and should be sent to the other peer via signaling as soon as possible to get the best connection route possible.

> The received ICE candidates should be accepted only after an OFFER/ANSWER exchange has happened. And handling the ICE candidates is the place where one would find issues working with WebRTC most of the time.

# </> 

![](https://miro.medium.com/max/700/1*8ZhZ5muYnkz7u2duz4NqzA.png)

And thus, a successful peer-to-peer connection has been established in WebRTC. 


### Reference

>  [w3schools](https://www.w3schools.com/html/html5_semantic_elements.asp)

>  [MDN
 docs](https://developer.mozilla.org/en-US/docs/Glossary/Semantics#semantics_in_html)
>  [freecodeCamp](https://www.freecodecamp.org/)