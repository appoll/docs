Network Server
==============

## Role

A network server is a component dedicated to the network management. It handle the required
logic to handle and to emit MAC commands (more precisions about MAC commands are given below).
Basically, a network server is composed of two parts:

- a core logic
- a data storage

The former interprets commands sent by nodes, forwarded by routers and brokers, combine them
with data stored in the latter in order to give an appropriate answer. The main usecase of the
network server will concern the adaptive data rate (*ADR*) control detailed in the [LoRaWAN
specifications 1.0][lorawan] - section 4.3.1.1 Adaptive data rate control in frame header. 

In order to keep the network highly reactive, we'll adopt an in-memory storage. The network
server will make sure data are persisted regularly in order to both prevent from small
disruptions and recover from an existing state after an update or a maintainance activity.

A network server is associated to a broker. The broker is forwarding, queuing and dispatching
packets whereas the network server interpret them when necessary. Because a network server is
only having interactions with a dedicated broker, the set broker + network server could be
seen as one unique component with a bigger but well-defined set of responsibilities. This
also implies that a given network server only have one and only one correspondent. There is no
transversal communication between network server. The storage isn't distributed over the
network but rather fragmented over it. 

<span class="label label-info">discussion</span>   
> In future versions, it could be nice for reliability to make data of network servers
> accessible from anywhere in the network. This nevertheless raises an issue about
> confidentiality and confidence over all network constituents. 




[lorawan]: https://www.lora-alliance.org/portals/0/specs/LoRaWAN%20Specification%201R0.pdf
