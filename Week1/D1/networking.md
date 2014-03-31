## How the internet works (OSI MODEL)

### What is a network?

It is a connection between computers, either physically or wirelessly. Internetworking is a connection between networks.

If you have three computers connected in a network, there is one computer, called a switch or a hub, through which the other computers communicate. Imagine there are two networks and you'd like to communicate between them. This requires a router between them in order to communicate. 

![Router communication](https://lh3.googleusercontent.com/EQk3B2P1CHaoqNjK13vObWFtXzJI2dyL9GSDEAmdqLsgdiEod-G8B4OvUVmELFKtjyjII5A5gk6pIvzZo3mmgBgCSKDoN2hR1oS31KB27OtXuokXvIoOJDdOVA)

###OSI Model

The OSI, or "Open System Interconnection", model defines a networking framework to implement protocols in seven layers. The OSI Reference Model is really just a guideline. Actual protocol stacks often combine one or more of the OSI layers into a single layer.

![OSI Model](https://lh6.googleusercontent.com/nbRf4uhJAs5Q1zf0QYFgPKnkGIpggQiFQ7CmNZGliL_bu34ZO6TQeVUFOD_2L3J7PPFM_NH6BNsJyVxVExnoA_7oY9xeTKatDz5t3j3LpsrAgBrL-tw2w-kHUA)

**Application Set**

Layer 7: Application - This is the layer that actually interacts with the operating system or application whenever the user chooses to transfer files, read messages or perform other network-related activities.

Layer 6: Presentation - Layer 6 takes the data provided by the Application layer and converts it into a standard format that the other layers can understand.

Layer 5: Session - Layer 5 establishes, maintains and ends communication with the receiving device.

**Transport Set**

Layer 4: Transport - This layer maintains flow control of data and provides for error checking and recovery of data between the devices. Flow control means that the Transport layer looks to see if data is coming from more than one application and integrates each application's data into a single stream for the physical network.

Layer 3: Network - The way that the data will be sent to the recipient device is determined in this layer. Logical protocols, routing and addressing are handled here.

Layer 2: Data - In this layer, the appropriate physical protocol is assigned to the data. Also, the type of network and the packet sequencing is defined.

Layer 1: Physical - This is the level of the actual hardware. It defines the physical characteristics of the network such as connections, voltage levels and timing.

![OSI Reference Model](https://lh4.googleusercontent.com/KCd53f62CzL5sDOJRUZPj0oUT8uRnNO6VUpQ34R9-BbS1PbvCu2KDBCfbRnNFAlCpJ82adtXHTRgq3DufpYzRChN7Frh1kCghB4iq-uLnn_YzNGMPEaHolL40g)

**IP Address**

Internet Protocol version 6 (IPv6) is the latest revision of the Internet Protocol (IP), the communications protocol that provides an identification and location system for computers on networks and routes traffic across the Internet. IPv6 was developed by the Internet Engineering Task Force (IETF) to deal with the long-anticipated problem of IPv4 address exhaustion.

Every device on the Internet must be assigned a virtual address in order to communicate with other devices. Each computer has such a virtual address, called an IP address in this format: 
`192.168.0.1.`

The numbers between each . are octets. There can be 2^32 IP addresses (and a few are reserved for specific uses, leaving c.4 billion in IPv4). With the ever-increasing number of new devices being connected to the Internet, the need arose for more addresses than IPv4 is able to accommodate. IPv6 uses a 128-bit address, allowing 2^128, or approximately 3.4Ã—1038 addresses, or more than 7.9Ã—1028 times as many as IPv4, which uses 32-bit addresses.

IPv6 addresses are represented as eight groups of four hexadecimal digits separated by colons, for example:   
`2001:0db8:85a3:0042:1000:8a2e:0370:7334 `  
... but methods of abbreviation of this full notation exist.

IPv6 is intended to replace IPv4, which still carries the vast majority of Internet traffic as of 2013. As of September 2013, the percentage of users reaching Google services over IPv6 surpassed 2% for the first time.

The two protocols are not designed to be interoperable, complicating the transition to IPv6.

Rather than remembering all the IP addresses for all the websites we visit, we use DNS. The Domain Name Service maps IP addresses to words - which is easier for people to understand, remember, and type. 

HTTP (Hypertext Transfer Protocol) is a protocol which is used to transfer web content. When a web client contacts a web server, HTTP allows the client to call methods on the server (similar to methods in our code). HTTP maps nicely with CRUD (Create - Read - Update - Destroy) which we will be using.

Operation | SQL | HTTP
----------|-----|------
Create | INSERT |POST
Read (Retrieve) |SELECT | GET
Update (Modify) |UPDATE |PUT / PATCH
Delete (Destroy) |DELETE | DELETE

### URL/URI

A URI (Uniform resource identifier), is a  is a string of characters used to identify a name or a web resource. 

A URL(Uniform resource location) is a string that constitutes a reference to a resource

**Elements of a URL**

 http://www.example.org/hello/world/foo.html?foo=bar&baz=bat#footer  
 
 http:// (protocol) www.example.org (host/domain name) hello/world/foo.html (path) ?foo=bar&baz=bat (querystring) #footer (hash/fragment)


 Element | About
 ------|--------
protocol | the most popular application protocol used on the world wide web is HTTP. Other familiar types of application protocols include FTP, SSH, HTTPS
host/domain name | the host or domain name is looked up in DNS to find the IP address of the host - the server that's providing the resource
path | web servers can organise resources into what is effectively files in directories; the path indicates to the server which file from which directory the client wants
querystring | the client can pass parameters to the server through the querystring (in a GET request method); the server can then use these to customise the response - such as values to filter a search result
hash/fragment | the URI fragment is generally used by the client to identify some portion of the content in the response; interestingly, a broken hash will not break the whole link - it isn't the case for the previous elements