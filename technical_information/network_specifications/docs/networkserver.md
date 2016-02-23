Network Server
==============


NS          | Description
------------|------------
NS-1        | Handle commands from a broker
NS-2        | Monitor the network
NS-3        | Emit commands to a broker
NS-4        | Handle OTAA & new node
            |
NS-1.1      | Listen and receive incoming command messages
NS-1.2      | Identify related command
NS-1.3      | Execute command
NS-1.4      | Reply if necessary with another command
            | 
//TODO


## Role

A network server is a component dedicated to the network management. It handles the required
logic to handle and to emit MAC commands (more precisions about MAC commands are given below).
Basically, a network server is composed of two parts:

- a core logic
- a data storage

The former interprets commands sent by nodes, forwarded by routers and brokers, combines them
with data stored in the latter in order to give an appropriate answer. The main usecase of the
network server will concern the adaptive data rate (*ADR*) control detailed in the [LoRaWAN
specifications 1.0][lorawan] - section 4.3.1.1 Adaptive data rate control in frame header. 

In order to keep the network highly reactive, we'll adopt an in-memory storage. The network
server will make sure data are persisted regularly in order to both prevent from small
disruptions and recover from an existing state after an update or a maintenance activity.

A network server is associated to a broker. The broker is forwarding, queuing and dispatching
packets whereas the network server interprets them when necessary. Because a network server is
only having interactions with a dedicated broker, the set broker + network server could be
seen as one unique component with a bigger but well-defined set of responsibilities. This
also implies that a given network server only has one and only one correspondent. There is no
transversal communication between network servers. The storage isn't distributed over the
network but rather fragmented over it. 

<span class="label label-info">discussion</span>   
> In future versions, it could be nice for reliability to make data of network servers
> accessible from anywhere in the network. This nevertheless raises an issue about
> confidentiality and confidence over all network constituents. 

Moreover, the network server has also an active role during an over-the-air activation (join
request) from a node: it allocates a new randomly generated device address. Because node
addresses within the network are 24 bits length, there is a 100% chance to allocate the same
address to two different nodes after more or less 5500 allocations. However, all packets also
carry a MIC number, and thus there is still a way for the network to clearly identify a node
without any risk of collision. 


### MAC Commands

There is a set of fourteen (in fact, 7 commands and 7 acknowledgements) MAC commands that will
transit over the network and with which it should be compliant. A MAC command materializes an
exchange between the network (via the network server) and the MAC layer of an env-device. This
means that those commands are invisible for the application server registered to a handler, but
also for the one embedded on the device.
These commands serve the network internal management: 

Command         | Transmitted by        | Description 
----------------|-----------------------|------------
LinkCheckReq    | End-device            | Used by an end-device to validate its connectivity to a network
LinkCheckAns    | Network Server        | Answer to LinkCheckReq command. Contains the received signal power estimation indicating to the end-device the quality of reception (link margin).
LinkADRReq      | Network Server        | Requests the end-device to change data rate, transmit power, repetition rate or channel.
LinkADRAns      | End-device            | Acknowledges the LinkADRReq
DutyCycleReq    | Network Server        | Sets the maximum aggregated transmit duty-cycle of a device
DutyCycleAns    | End-device            | Acknowledges a DutyCycleReq command
RXParamSetupReq | Network Server        | Sets the reception slots parameters
RXParamSetupAns | End-device            | Acknowledges a RXParamSetupReq command
DevStatusReq    | Network Server        | Request the status of the end-device
DevStatusAns    | End-device            | Returns the status of the end-device, namely its battery level and its demodulation margin
NewChannelReq   | Network Server        | Creates or modifies the definition of a radio channel
NewChannelAns   | End-device            | Acknowledges a NewChannelReq command
RXTimingSetupReq| Network Server        | Sets the timing of the reception slots
RXTimingSetupAns| End-device            | Acknowledges a RXTimingSetupReq command

*The above table is extracted from the [LoRaWAN specifications][lorawan] - Section 5 MAC Commands.*

Technically, only one "real" command is emitted by the node, the rest is only acknowledgement
and data transmission. Commands are mainly initiated by the network server and all of them are
detailed in the LoRaWan Specifications.

### Communication with the broker

In practice, the network server and the broker are likely to be localised on the same physical
entity. However, in order to be able to scale easily in the future and to decouple those
components if needed, they will talk to each other through well defined interfaces. In the same
pattern we are using for any component, the network server will be divided in three
sub-components:

- The storage that provides an interface to the core logic to store and retrieve stored data.
- The core logic, which manages commands and triggers actions on the other sub components.
- The broker adapter, which communicates with the broker and triggers actions on the core logic
  when commands are forwarded by the broker.

## Interfaces

### Storage
// TODO

### Core
// TODO

### Broker adapter
// TODO


[lorawan]: https://www.lora-alliance.org/portals/0/specs/LoRaWAN%20Specification%201R0.pdf
