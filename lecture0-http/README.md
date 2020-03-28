# HTTP

### High level overview

Computer makes a request through modem to ISP to reach google.com's servers and they reply with a response:

- When a user hits enter, www.google.com is translated into an IP address, which is a numerical identifier of the form w.x.y.z for a server or computer on the internet. An IP address is represented by 32 bits, each of those 4 sections using 8 bits.
- The user's computer finds the IP address via a domain name lookup through a DNS (domain name system). A DNS converts domain names and host names to IP addresses and vice versa.
- A home computer usually does not have it's own DNS server, but a university, company, or ISP often does. There are levels of DNS servers in a sense. If the user is visiting a website for the first time and the nearest DNS server does not know the translation of domain name to IP address, it will bubble up and ask the next level DNS what the translation is. At the top of hierarchy, there are root servers across the world that always know who to ask.
- Once the user's computer knows for sure the IP address, it puts together a message to send across the internet to google. This is an http request of the format "GET / HTTP/1.1" along with a number of headers. This message is a virtual envelope called a packet. Inside the packet is the message with the request options and on the outside of the packet is the to and from IP addresses.
- Your ISP has a default gateway or router; these are computers that know how to get data from point A to point B. If they don't know they know who to ask. Packets are passed from router to router until they reach their ultimate destination.
- Once the packet reaches its destination, the webserver opens it up and sees the message inside the packet. The webserver looks for the file called "/" (given the message above), grabs it from it's hard drive, puts it in a packet and flips the IP addresses to send the response.
- The packet is sent back over the internet via these routers until it reaches the user's computer, the computer opens the envelope and finds the content (in this case for google.com and "/"), which is a bunch of HTML and so it renders the HTML and the search page for google.com is seen on the screen.

## URL

http://www.google.com/
http:// - the protocol or schema (others include https://, ftp://, sftp://), indicates to software how to view the contents at that address
cs - subdomain name (like in cs.utexas.edu)
google.com - domain name
.com - top level domain (.edu, .gov, .uk)
/ - path (in this case this is the root)

### One IP address

- A home network often shares one IP address
- Multiple computers on the same home network still need their own IP address, so computers on this network have a private IP address (192.168.x.x, 172.16.y.y, 10.z.z.z). A website hosted on a private IP address cannot be accessed from outside that home network.
- Because a private IP address isn't unique enough to be a real IP address, you can use port forwarding.
- HTTP uses other protocols like TCP/IP (transmission control protocol and internet protocol). IP is just the standard for assigning computers addresses. Getting data to that address is another issue, that is handled by TCP. TCP is the protocol followed by servers to physically move data from point A to point B using the idea of identifying point A and point B via IP addresses.
- TCP/IP allows the web, email, instant messaging, etc to work, it is not limited to within a browser.
- A server can do multiple things, like receive email, handle http traffic, etc, so it needs port numbers to identify the various services it can offer.
- A web server using tcp behind http uses port 80 to identify the web site service.
- An email server on the same server would use port 25. Emails now container HTML, so it's important to distinguish it from regular traffic via the port number.
- Port forwarding is configuring internet traffic coming in on a public ip address on a particular port to be redirected to a private ip address
- https uses port 443

### DNS

- Where you buy your domain name doesn't matter but you can get different features/service offerings based on where it's from such as free email accounts or hosting
- Once you purchase a domain, you need to tell the world what the ip address is by telling the registrar the domain name's dns server ip address. Convention is every domain name has two dns servers, primary and secondary.
- This is where web hosting companies come in. A web host provides storage space, connection to the internet, a pool of ip addresses, memory, etc to keep a server alive on the internet. They should also provide two dns servers to provide a primary and secondary ip address.
- Sometimes a website will work at www.something.com but not at something.com. To fix this, there must be a DNS record (IP address) for "something.com", in addition to the one for "www.something.com". The web server must also be configured to accept for either www.something.com or something.com.
- A DNS server is a database with rows and columns of domain names and ip addresses. There are also rows of NS, A, CNAME, etc:
- NS - Name Server Record - Record of server providing DNS services for that domain name; domain name to another name server
- A - A Record - Record of domain name to IP address
- CNAME - Canonical name - An alias, maps domain name to another domain name
- MX - Mail exchange record - IP address of server(s) that should handle inbound mail for this domain name

### Web Hosts

- Many web hosts offer shared services, which use virtual hosting which allows multiple web sites to be hosted on the same web server and IP address. This requires the browser to remind the server in the GET request what the website request was for to return the appropriate webpage.
- This has some implications: if the server crashes all the sites are affected. There is contention for resources, so if a site on the shared host is super popular that could affect other sites. Or a denial of service attack on one site could affect all sites. There is also no control over the server, it is being controlled and managed by other people. There is little control over what php version, or node version, etc.
- Virtual Private Servers (VPS) are an alternative to the private hosting model. A VPS is a dedicated (or illusion of one) server to yourself. Virtualization using a hypervisor can allow multiple servers to be run on a single machine. On top of this software like VMWare or Citrix you can install multiple versions of windows or linux, giving the illusion of multiple computers each with their own usernames, passwords, admin rights, etc. They are sharing the hardware but not the software.

### SSH, SFTP, DHCP

- SSH - Secure shell - old school secure way of connecting to a remote server and executing commands on it
- SFTP - SSH file transfer protocol - secure file transfer protocol
- DHCP - Dynamic host configuration protocol - dynamically provides an ip address to a machine when the machine asks for it, on a virtual machine or on a network with a modem or router

### HTTP GET

- Typically when the url changes after a user request, it is a GET request
- https://www.google.com/search?q=harvard
- The /search denotes a particular file on the server
- The '?' separates the path from the parameters
- In a url request, q is an http parameter, the query parameter, an input to a web server than generally comes from a form (or javascript code)
- Parameters are separated by an '&'

### Developer Tools

- Elements tab shows page html in pretty print and in it's hierarchy
- Network tab sniffs all the traffic between the browser and server and show each http request, one per line
- There are many http requests because the initial http request loads a page that requests additional resources, such as more javascript, images, etc
- Within each http request line there we show the headers (), the preview (what was returned), the response (the raw of what was returned)

### Telnet

Used in terminal to explore websites outside the scope of a browser. Like ssh but not encrypted.
code(telnet www.google.com 80)
code(GET / HTTP/1.1)
Hit enter twice.
Can help with debugging.

www.harvard.edu vs harvard.edu
Cookies can be forced to be contained on www.harvard.edu if you require www.harvard.edu by the browser. If you are more flexible with allowing just harvard.edu, cookies can be accessed from cs.harvard.edu, somethingelse.harvard.edu, etc.

### NSLookup

DNS lookups, lookup an IP address for a given website
code(nslookup www.google.com)

### /etc/hosts

A file on macs and linux that maps/hardcodes domain names

### SSL

A SSL certificate is required to get https. If you aren't using https, nothing is encrypted. Without https, session hijacking attacks are much easier. With just http, cookies are not encrypted, so an attacker can sniff the cookie and send it as their own to login as you on your session.

Even websites that redirect from the unencrypted version to the encrypted version can be vulnerable, because these websites can send the cookie in the clear before the redirect and encrypted version.
