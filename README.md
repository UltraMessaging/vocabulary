# vocabulary

This document defines terms related to Ultra Messaging. Knowing these terms is useful when communicating with Informatica and other users about UM. A more complete glossary can be found here:
https://ultramessaging.github.io/currdoc/doc/Design/umglossary.html

# General Terms

**Ultra Messaging (UM)** - name of product family.

**LBM** - old name for Ultra Messaging. Lives on as API prefix. Also sometimes used to refer to the UM library layer of software, as in "LBM configuration" (as opposed to "Store configuration"). Also sometimes used to refer to the streaming-only product offering (now called "UMS").

**29West** - company that created UM in 2004. Merged with Informatica in 2010. Many people still use the term "29West" to refer to the product itself.

**Streaming** - the basic UM mode of operation. Features low latency and high reliability (lost packet recovery in real-time).

**UMS** - UM product name that offers streaming mode (only).

**Persistence** - UM resilient mode of operation that features higher quality of service (fault tolerance), allowing lost message recovery after component failures.

**UMP** - UM product name that offers streaming and persistence modes.

**UME** - old name for UMP. Lives on as API prefix.

**Queuing** - UM mode of operation that offers first-in, first-out queuing semantics. I.e. multiple sources to the same topic are intermingled with ordering retained. Also supports "once and only once" consumption and load balanced subscribers. UM offers persisted queues (leveraging ActiveMQ) and light-weight load balancing ("ULB").

**UMQ** - UM product name that offers streaming, persistence, and queuing modes.

**Publish/Subscribe (PUB/SUB)** - a message-based communication model whereby senders publish messages to abstract destinations called "topics" (sometimes called "subjects" with other products), and receivers subscribe to those same topics. The messaging layer is responsible for getting messages published on a given topic to all subscribers of that topic. 

**Topic** - a string (max 250 characters). With UM, the topics are not hierarchical (e.g. "/" and "." are not special).

**Source (SRC)** - software object, created by an application, and associated with a topic. Used to publish messages on a topic. Note that it is possible to publish messages on a topic that has no subscribers. This is not considered an error.

**Receiver (RCV)** - software object, created by an application, and associated with a topic. Used to subscribe to messages published on a topic. Note that it is possible to subscribe to a topic that has no publishers. This is not considered an error.

**Wildcard Receiver (WRCV)** - software object, associated with a regular expression topic pattern, created by an application. Used to subscribe to one or more topics that match the pattern. Note that it is possible to supply a pattern that matches no existing publisher topics. This is not considered an error.

**Context (CTX)** - software object, created by an application. It is an "instance" of UM. An application will create one or more sources and/or receivers in a context. Most applications create a single context, but there are less-common use cases where multiple contexts are useful.

**Topic Resolution (TR)** - protocol and software layer used by Ultra Messaging to support the proper delivery of messages from publishers to subscribers. By default, TR users multicast.

**Stateful Resolution Service (SRS), LBM Resolver Daemon (lbmrd)** - UM components that support topic resolution without using multicast.

**LBT-RM** - Reliable Multicast transport type. One of several transport protocols to choose from. There's also LBT-RU (Reliable Unicast), TCP, IPC, and SMX (the latter two are shared memory).

**Transport Session** - an active instance of a transport type. A publisher might be sending on many topics, and map them across a few transport sessions.

**NAK** - Negative Acknowledgement. Sent by subscribers of LBT-RM and LBT-RU protocols to recover lost packets via the transport protocol.

**OTR** - Off-Transport Recovery. Alternate lost packet recovery mechanism when the normal NAK-based recovery mechanism fails or times out.

**Late Join** - feature by which a subscriber can recover messages that were sent before the subscriber joins a source's message stream.

**BOS, EOS** - Beginning Of Session, End Of Session. Events delivered to a subscriber when a publisher's transport session is joined and exited.

**Datagram** - usually means a UDP message. Sometimes in UM we extend its use into non-UDP transports.

**SQN** - Sequence Number. A number assigned to a datagram to support lost packet detection. There are two layers of sequence numbers: transport and topic. Transport-level SQNs are not visible to application programs and are usually ignored by users (unless troubleshooting network issues). Topic-level SQNs are visible to application programs.

**UIM** - Unicast Immediate Message. A TCP-based protocol, separate from transports, typically used by UM for internal command and control messages.

 
# Pesistence-Related Terms

**Store** - component that persists messages, typically to disk. If a user subscriber fails and restarts, it uses the Stores to recover messages that were sent while it was down. Note that using persistence greatly extends the amount of saved data available for late join and OTR. For fault-tolerance, multiple Stores are usually deployed to operate in parallel.

**Quorum** - when over half of the persistent Stores are operational. The number of stores configured must be odd. Three is most common, five is sometimes used. So quorum means 2 of 3, or 3 of 5.

**Repository** - file/memory used by the Store to save messages from a source. Note that if multiple publishers create a source for the same topic, the Store will create a separate repository for each source.

**Stable** - a published message is considered stable when a quorum of Stores has successfully written the message to disk. Store components deliver stability events to publishers as messages are written to disk.

**Flight** - a message that has been published but not yet reported as stable is said to be "in-flight".

**Consumption** - when a subscriber has received and fully processed a message.

**RPP, SPP** - Receiver-Paced Persistence, Source-Paced Persistence. Two modes of operation for Stores. The modes behave differently in several important ways.
