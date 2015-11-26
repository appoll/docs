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

Also, for the rest of this document, we'll described handlers as a completely independant
component that interacts with both a broker and an application. In practice, handlers could be
partially merged with an application or be provided as a cloud service. This does not change
anything for the network point of view as long as handlers remain compliant with the uplink
interface detailed below. 

Having said that, the handler is supposed to perform the following actions:

- Register an application and a bunch of personnalized devices and DevEUI
- Register to a broker (with an application EUIs, 
- Receive and decrypt incoming packets from a broker with deduplication mechanisms
- Handle join request (generate NwkSKey, AppSKey, AppNounce...)
- Forward packet's payload to its application
- Forward response from an application to brokers
