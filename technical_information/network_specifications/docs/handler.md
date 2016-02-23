Handler
=======

## Role

The handler is the last buffer between the network and the app. Thus, it is in charge of doing
anything private to the app. Handlers are a bunch of recurrent services that any app will need
to interact with the network. Instead of having to implement all the mechanisms described
below, applications can just trust a given handler to manipulate their secret keys and handle
the overhead logic of packet encryption, registration and decryption. 

This raises nevertheless an important trust issue. The network is open, meaning that anyone can
join it and contribute with their own servers. The code is thereby accessible such that it
simplifies the installation of a component. However, The Things Network isn't a certificate
authority of any kind, meaning that we cannot ensure that a given broker implement a code
strictly identical to the one provided by the foundation. The question is still open and
discussion are welcomed. 

Also, for the rest of this document, we'll described handlers as a completely independent
component that interacts with both a broker and an application. In practice, handlers could be
partially merged with an application or be provided as a cloud service. This does not change
anything for the network point of view as long as handlers remain compliant with the uplink
interface detailed below. The following proposal states one kind of handler provided in the
first version of the network. Different implementations with different mechanisms could exist,
however, this document only refers to one type. 

Having said that, the handler is supposed to perform the following actions:

- Register an application and a bunch of personalized devices and DevEUI
- Register to a broker (with an application EUIs, 
- Receive and decrypt incoming packets from a broker with deduplication mechanisms
- Handle join request (generate NwkSKey, AppSKey, AppNounce...)
- Forward packet's payload to its application
- Forward response from applications to brokers

Again, this component acts as a relay between some recipients. Let's detail in the
communication in two parts and distinguish uplink communication from application requests. We
won't talk about downlink communication because the handler materalize the end of the chain
and, the downlink communication is part of the uplink process for the handler point of view.
The handler is managing the communication back and forth between the network and the
application. However, the application can still operate on a configuration level with the
broker (so far, only to register / unregister itself). 

### Handler configuration

Initially, the network is completely unaware of any application meaning that any messages
emitted by gateways won't go further than routers. However, at any time, an application can
connect to the network by interacting with a handler. 

To join the network, the application has to provide several private data such as:

- An application id
- A secret application key
- A list of known DevEUI 
- Way back information (protocol + server)

With those pieces of information, the handler would be in charge of all the traffic towards the
given application. An application could possibly update the list of nodes or the communication
protocol later. 

There is no reason for a handler to reject an application join request for the moment however,
the handler will still reply with a positive response to the willing application. 

### Uplink communication

We'll call uplink communication any communication coming from brokers. Uplink communications
could be either data coming from a node or a join request.  

Because of the network architecture, several duplicates of the same messages could arrive to
the handler. We assume that all of those messages are arriving all in the same time. The
handler will then have to deduplicate all messages if necessary and proceed to a geolocation
based on the information provided by the gateways. The handler also has access to the
application secret key, Thus, it could decrypt the data and forward it to the application. 
