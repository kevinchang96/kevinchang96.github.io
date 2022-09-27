---
layout: post
title: "OSI 7-Layer Model"
author: "Kevin Chang"
date: "2020-02-04 17:00:00"
update_date: "2022-09-25 17:00:00"
excerpt_separator: <!--more-->
---

The Open Systems Interconnection model (OSI model) is a conceptual model developed by the International Standards Organization (ISO) to define the functions and responsibilities of a networking system. The system is divided into seven separate abstract layers, each relying on the abstract layers below it in order to fulfill its own specific job and protocol. <!--more-->The layers are illustrated bottom-up as follows:

|Number|   | Layer Name |
|:----:|---|------------|
|   1  |   | physical   |
|   2  |   | data link  |
|   3  |   | network    |
|   4  |   | transport  |
|   5  |   | session    |
|   6  |   | presentation|
|   7  |   | application|

<br>
Note that the OSI model is bidirectional, so while a top-down explanation is possible, we choose to proceed with a bottom-up exploration of the model, as it is understood each layer builds upon the functionality of the previous. We begin with the physical layer.

### Physical Layer

The **physical layer** is responsible for the communication of raw, unstructured signals between devices through a physical transmission medium. Think of a stream of `0s` and `1s` (signals). This layer has four main mechanisms through which it provides this transmission between devices: `bit synchronization`, `bit rate control`, `physical topologies`, and `transmission mode`.

The first two deal with the transmission protocol such that unstructured raw data can be made intelligible and meaningful. Specifically, the physical layer includes a clock and defines the signal transmission rate. The clock provides the ability for the sender and receiver to synchronize signal transmission at the bit level and the transmission rate standardizes the number of signals sent per second within the network.

The last two mechanisms deal with the hardware. Specifically, physical topologies defines the placement of devices within a network while the transmission mode defines the signal flow between two connected devices. These four mechanisms ensure that raw, unstructured signals can be transmitted amongst devices within a local network.

### Link Layer

The data **link layer** is responsible for the formalization of data transmission between devices within a local network. This data link layer is divided into two sublayers: the `Logical Link Control (LLC)` and the `Media Access Control (MAC)` sublayers.

The LLC sublayer is responsible for identifying and enforcing network layer protocols, frame synchronization, flow control, and error checking. The MAC sublayer is responsible for establishing authority or permission for which devices is allowed to send and receive data—at the given moment—on a single communication channel that is shared by multiple devices.

In more detail, the data link layer takes the signals from the physical layer and packages them into a frame by attaching a unique, predetermined bit patterns at the beginning and end of a frame. After creating the frame, the physical addresses (MAC address) of the sender and receiver are added to the frame header. Through this, the data link layer can provide frame synchronization, flow control, and error checking whereby the data layer maintains a constant data flow and strict protocol so that deviations in the flow or protocol can help to detect and recover from simple data corruption. In simplest terms, data frames are encoded/decoded out of/into bits at the data link layer.

### Network Layer

The **network layer** is responsible for the transmission of packets between devices in different networks. Packets encapsulate frames as they contain additional identifying information (IP address) regarding a particular device and the network it belongs to. Since this layer transmits packets across different networks, this layer is responsible for finding the best available physical path to the destination; also known as routing. Additionally, address assignment, encapsulation and fragmentation, inter-networking, error handling, congestion control, and packet sequencing are also part of this layer’s responsibilities. This is the layer that includes protocols such as IPv4 and IPv6.

### Transport Layer

The **transport layer** is responsible for end-to-end communication between devices. The layer is also responsible for flow/congestion control and error control wherein the optimal transmission rate is used based upon acknowledgement of packet reception and a retransmission is requested if the received data is incomplete or corrupted. The most well-known protocols in this layer is the `Transmission Control Protocol (TCP)` and the `User Datagram Protocol (UDP)`. Subsequent posts will discuss [TCP]({% post_url 2020-02-16-TCP %}) and [UDP]({% post_url 2020-02-18-UDP %}) in greater detail.

### Session Layer

The **session layer**, as the name describes, is responsible for establishing, maintaining, and terminating a connection between devices. This layer also synchronizes data transfers with checkpoints such that if there is a connection failure, data loss is avoided and transmission can properly resume at the appropriate last checkpoint after re-synchronization.

### Presentation Layer

The **presentation layer**, also known as the translation layer or the syntax layer, is responsible for mapping data between application and network formats. Encryption/decryption of data for secure transmission in transit, character code translation (ASII from/to EBCDIC), data conversion, and data compression are examples of the presentation layer at work.

### Application Layer

Finally the **application layer** is the layer that users directly interact with. Noteworthy is that client software applications are not considered part of the application layer. Rather it is the protocols and data manipulation that the software relies on that is part of the application layer such as HTTP, SMTP, FTP. This layer “identifies networking entities to facilitate networking requests […] determines resource availability, synchronizes communication, manages application-specific networking requirements [and] identifies constraints at the application level such as those associated with authentication, privacy, quality of service, networking devices, and data syntax.”

The OSI model can seem like a confusing mashup of technical buzzwords, but after a few years in tech, you’ll be able to spout off these buzzwords confidently. At the end of the day, just remember that **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing and that **P**ete **D**oesn’t **N**eed **T**o **S**ell **P**ickles **A**nymore. 

---

|References|
|---|
|[Wikipedia](https://en.wikipedia.org/wiki/OSI_model)|
|[NetworkWorld](https://www.networkworld.com/article/3239677/the-osi-model-explained-how-to-understand-and-remember-the-7-layer-network-model.html)|
|[Webopedia](https://www.webopedia.com/quick_ref/OSI_Layers.asp)|
|[GeeksForGeeks](https://www.geeksforgeeks.org/layers-of-osi-model/)|
|[BMC](https://www.bmc.com/blogs/osi-model-7-layers/)|
|[CloudFare](https://www.cloudflare.com/learning/ddos/glossary/open-systems-interconnection-model-osi/)|
