---
title: 'Network + Study Notes: Open System Interconnection (OSI) Network Model'
cover:
 image: "/uploads/2021/06/Home.jpg"
 alt: ""
 relative: false
 hidden: false
 hiddenInList: false
 hiddenInSingle: false
author: SarCoptU
date: 2021-06-13T07:00:00+00:00

categories:
 - IT/Cybersec
tags:
 - Computer Networking
 - OSI Model
 - TCP/IP
---
Computers have a very particular way of communicating with each other. For example, before widespread computer networks and the internet, people shared files by &#8220;sneakernet&#8221;: wearing a comfortable pair of sneakers and putting the info on the storage media and travelling to the destination computer to copy the data.

This is not a very effective way to share files, and large companies soon realised this was a huge market and started developing various network protocols. The problem was that many of these protocols were proprietary and incompatible with each other.

In an industry effort to play nice with each other, in 1984, the International Standards Organisation (ISO) published a seven-layer model to define computer systems&#8217; communication function and standardise interoperability between future networks.

The 7 layers of the OSI model, closely observed by Sigi, are:<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="959" src="/uploads/2021/06/Home.jpg" alt="OSI Network Model" class="wp-image-226" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>OSI Model</figcaption></figure> 

A mnemonic to remember the layers in the above order: &#8220;All People Seem To Need Data Processing&#8221;.

A few things to note before diving into the layer description:

To understand better the layered fashion of networks, we have to discuss encapsulation. In Russian doll fashion, this process takes a protocol and hides it inside another protocol to travel on the network more easily. Similarly, the data from the application layer (7) gets encapsulated into the segments at the transport layer (4), which get encapsulated into the packets at the network layer (3), which finally are hidden into the frames at the data link layer (2). The different names for the information (data, segments, packets, frames) are collectively known as Protocol Data Units (PDU). Once the data arrives at the receiving computer, the process is reversed from layer 1 to layer 7. Thus, the functions of layers are equivalent and sometimes opposite on the sending and receiving computers, respectively, like compression/decompression or segmentation/reassembly.

The layers only communicate with adjacent layers and each layer has a specific function. This provides a certain degree of modularity to the stack. It also helps with troubleshooting the network because the issue can be pinpointed to a specific layer.

The OSI model is still a great way to define networks, but it has begun to show its age. It was developed almost 40 years ago, which in computer terms is a few aeons. It&#8217;s no wonder then that the protocols have evolved since then and don&#8217;t fit nicely into one layer, quite a few spreading to a few layers or even the whole stack.

Let&#8217;s have a look at all the layers and provide a bird&#8217;s eye view of their functions:

### **Layer 7: Application** {.wp-block-heading}

All the networking capability of our day to day applications are taken care of at this layer, and it&#8217;s the most visible layer for the user. For example, when we browse a web page, share a file or send an email, the application layer comes into play and gives the web browser or the social media app its networking flavour by providing an interface to communicate with the network. The application in this context does not refer to Chrome, Mozilla or Facebook, but to the API (Application Programming Interface) that gives the developer the option to integrate networking functionality into the aforementioned programs.

### **Layer 6: Presentation** {.wp-block-heading}

This is a translation layer in which the data is converted to the format required by the above or below layers. The type of formatting might be, for example, compression/decompression or encryption/decryption. Different types of data representation from the application layer are translated to the lower-layer network formats; in other words, the different application layer languages are translated to the unified language of the network stack.

### **Layer 5: Session** {.wp-block-heading}

The function here is to provide a channel of communication between computers. This layer provides clear rules to start, manage and end the dialogue gracefully. Thus, each session is a bridge between two programs on two computers, similar to how human switchboard operators used to connect different telephone customers.

### **Layer 4: Transport** {.wp-block-heading}

The sending computer fragments the data here (segmentation), gives a number to each fragment (sequencing) and makes sure the data is sent at an appropriate speed and when the destination computer is ready to receive (flow control). The receiving computer recreates the data (reconstruction), makes sure it has all the pieces and checks for errors and if it finds any, asks the sender to resend that particular piece of data (error checking). It sure is a lot of work to be done at this level. This is the Transmission Control Protocol (TCP) part of the layer.

To complicate things a bit more, there is also a method to send data unreliably: the sending computer sends all the pieces hoping that most of them arrive safely. The advantage is it&#8217;s a lot faster and works well for video and audio when losing a few milliseconds here and there usually goes unnoticed. This covers the User Datagram Protocol (UDP) part of the layer.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="336" src="/uploads/2021/06/TCP_vs_UDP.jpg" alt="TCP versus UDP quick explanation" class="wp-image-227" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>TCP vs UDP</figcaption></figure> 

A joke or two might clarify the difference:<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="449" src="/uploads/2021/06/tcp-joke.png" alt="TCP joke" class="wp-image-228" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>TCP</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="501" src="/uploads/2021/06/UDP-joke.jpeg" alt="" class="wp-image-229" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>UDP</figcaption></figure> 

### **Layer 3: Network** {.wp-block-heading}

Layer 3 deals with logical addressing and routing. The IP (Internet Protocol) address of a system is a software generated identity for that specific device. It&#8217;s similar to a postal address that gives information about the location of a computer inside a network. It can be changed or translated based on which side of the firewall is situated, the local area network (LAN) or the wide area (internet) network (WAN). You have definitely seen one before. It is a string of four numbers separated by dots, and it looks similar to 192.168.1.100, for example.

This is the layer where routers reside and function. Routers carry the burden of keeping up the biggest network in existence, the internet. And they do this with the help of IP addressed and another type of so called physical address, the Media Access Control address or MAC address.

TCP and IP are so important that a network model was developed to explain the overwhelming majority of network computing today, based mainly on these two protocols.

### **Layer 2: Data link** {.wp-block-heading}

The MAC address is a group of hexadecimal numbers (base 16) divided by hyphens, similar to 0F-AB-FC-90-64-9C. This is the above mentioned physical address burned on all devices with network capabilities and can never be changed as opposed to an IP address. Based on this address, devices like switches or the Network Interface Card (NIC) can communicate with directly connected computers. This is also the layer where the frame is converted to binary data (bits) and sent to layer 1 for transmission, or the reversed process on the receiving computer.

### **Layer 1: Physical** {.wp-block-heading}

The physical layer refers to the transport media. In a cable network, this means all the cables, as opposed to a wireless network, where the connectivity is provided by radio waves.

Wikipedia has a very comprehensive table on their OSI model wiki page:

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-full is-resized"><img data-recalc-dims="1" loading="lazy" decoding="async" src="/uploads/2021/06/OSI-wiki.png" alt="OSI model on Wikipedia" class="wp-image-230" width="750" height="290" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>OSI courtesy of Wikipedia</figcaption></figure>
</div>

It&#8217;s probably a good time now to provide an example:

Let&#8217;s say Mary from London wants to send an email to John in Manchester. First, she would open an email application, write the email and press send. Once she does that, the application layer (7) adds a header to the data using an email specific protocol and sends the data to the presentation layer (6). Here, the data is compressed, encrypted and delivered to the session layer (5). A connection session is established here with John&#8217;s computer, and then the data is taken over by the transport layer (4). The data is then broken down into segments, and each segment is given a sequence number and is then sent to the network layer (3). This layer adds a destination and a source IP address and encapsulates the segment into a packet. Next, the data link layer (2) adds destination and source MAC addresses to the packet, encloses it into a frame, and converts the frame into binary data (bits). Finally, the cables or radio waves of the physical layer (1) provide the transport media (helped by a few other internet devices) from Mary&#8217;s computer in London to John&#8217;s computer in Manchester.

At John&#8217;s location, the process is reversed: bits (layer 1) turn to frames (layer 2), then packets (layer 3) and finally segments (layer 4). The transport layer (4) checks all segments have arrived safely and reconstructs the data in the correct order, based on the sequence number. The session layer (5) helps by keeping the communication open to receive all the segments. The data is then sent up to the presentation layer (6), where it&#8217;s decompressed and decrypted, and finally, the application layer (7) presents the data to the email app, ready to be read by John.

Wow! That&#8217;s a lot of work just to send an email! The good news is that the magic behind all this is hidden from us by the network stack.

This was a very summarised view of the OSI model. It contains minimal information about specific protocols, and I certainly left many technical details out, but I think it serves the goal to understand better why the network stack was conceptualised this way. Still widely used today, the OSI Network Model provides an extremely useful mental framework for designing, constructing and troubleshooting networks.

References:

<ul class="wp-block-list">
 <li>
 Mike Meyers &#8211; CompTIA Network+ Certification All-in-One Exam Guide, Seventh Edition (Exam N10-007), 2018, McGraw-Hill Education
 </li>
 <li>
 Glen E Clarke &#8211; CompTIA Network+ Certification Study Guide, Seventh Edition (Exam N10-007), 2018, McGraw-Hill Education
 </li>
 <li>
 https://en.wikipedia.org/wiki/OSI_model
 </li>
 <li>
 https://en.wikipedia.org/wiki/Sneakernet
 </li>
</ul>
