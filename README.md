
# HTTP/2 and HTTP/3 Traffic Analysis 

## Objective

- Dissect and understand HTTP/2 traffic.
- Read hostnames, headers, URLs, and extracting files from HTTP/2 traffic.
- Understand how HTTP/2 and HTTP/3 differ from HTTP/1.1 and how tools will interact with them.
  

### Tools Used
 -Wireshark

## Steps

We will open a decrypted packet capture of multiple HTTP/2 and HTTP/3 web traffic. HTTP/2 can only be used with TLS encryption. 

#### HTTP/2
1. On Wireshark apply the search filter http2.


![Screenshot 2025-02-03 122937](https://github.com/user-attachments/assets/ffb4a366-3cd0-45dc-a13e-625d8bb9d4c9)

A noticeable difference compared to http traffic is that there is more displayed in the info column.

#### Frame Types
Another difference is frame types. The frame types can be found in the info column in the all capital letters. 
The frametypes: HEADERS, and DATA   
  - These are important because they carry HTTP headers and data going back and forth.
Multiple frametypes can be sent at once.
![Screenshot 2025-02-03 134507](https://github.com/user-attachments/assets/f59c741c-2af7-409e-b48d-ed41e9d8e584)


#### Stream identifiers and Following an HTTP/2 Session
Part of what makes HTTP/2 more efficient are all assets being downloaded from a server and mutiplexed into a single TCP stream accross multiple HTTP/2 streams. 
Each HTTP/2 stream has its own unique number (stream identifier), and is shown in [ ] square brackets after the frame type name. The numbers are unique within a given TCP stream in Wireshark and usually seen as increasing number starting from 0 and increasing upwards using odd integers. 

![Screenshot 2025-02-03 134904](https://github.com/user-attachments/assets/65df2498-ea22-4239-b977-e8d7c2b5b9ae)

This filter will show all traffic from a single TCP stream (#7) ... (this is not HTTP/2 stream identifier) ... 
<img width="377" alt="Screenshot 2025-02-03 135146" src="https://github.com/user-attachments/assets/a11f013f-f69c-4426-87b2-d6f693a3534a" />

The HTTP/2 stream identifiers highlighted are increasing. Because there are multiple different conversations happening inside a isingle TCP stream, HTTP/2 needs to use these numbers at the application layer to keep each discussion separate. 



Let's use the follow functionality on an HTTP/2 conversation. 

<img width="1086" alt="Screenshot 2025-02-03 135841" src="https://github.com/user-attachments/assets/376fd238-c456-46fd-8070-6a919c555efb" />

After selecting the HypterText Transfer Protocol dropdown in the packet details we see both frame types listed: HEADERS & WINDOW_UPDATE

2. Now lets righ-click on the packet and select Follow > HTTP/2 stream

![Screenshot 2025-02-03 140054](https://github.com/user-attachments/assets/0e1560c6-11c8-445d-8516-f77d72e143c1)
We see a lot of binary that is tough to read, much different from the HTTP/1.1 conversation. 

![Screenshot 2025-02-03 142314](https://github.com/user-attachments/assets/78ef4ca3-1b8c-4868-a7e8-834d61087603)

When we followed the session for packet #630 there will be a filter that is automatically applied. Now all square breackets in the list are 15


#### Viewing HTTP/2 Headers


![Screenshot 2025-02-03 143248](https://github.com/user-attachments/assets/e0338d43-32a2-46d9-96fa-da4abb59ee3b)

the HEADERS frames that contain the GET request and another for the response followed by a traditional 200 OK. The headers for a request are in separate frame types from the data sent related to that request. Separate

CLick the GET request frame and in packet details, unfold the Hyptertext Transfer pRotocol 2 line, then expand Stream: HEADERS, and scroll



<img width="1055" alt="Screenshot 2025-02-03 151542" src="https://github.com/user-attachments/assets/c1e48758-daaf-4a1d-98c5-d76ce94edcc9" />

Now we can view the HTTP headers. To locate headers for a request, we find the HEADERS frame for the stream ID of interest, then unfold that specific frame in the packet details. 
Another noticeable difference is for the domain name instead of bein Host: , it is not authority: 
We can take a look at that using it as a column for our data by right-clicking and apply as column. 



<img width="668" alt="Screenshot 2025-02-03 151944" src="https://github.com/user-attachments/assets/59d15a79-e53a-4a0f-9b63-19becdb9d30a" />


<img width="1125" alt="Screenshot 2025-02-03 152251" src="https://github.com/user-attachments/assets/075c7d6a-930e-4b0c-89d9-cbf1142c0218" />


### Extracting files from HTTP/2 Packet Capture

Wireshark does not support, unable to use Export Objects with HTTP/2

1. Bring up a single request to narrow in on.


![Screenshot 2025-02-05 082040](https://github.com/user-attachments/assets/ed962ebd-54e7-44ce-b525-feab8b56fd0b)


Now lets extract the file from this packet capture. 


<img width="1008" alt="Screenshot 2025-02-05 082350" src="https://github.com/user-attachments/assets/9778cf3e-0ed6-4e4c-8556-799896cf4dec" />


<img width="757" alt="Screenshot 2025-02-05 083652" src="https://github.com/user-attachments/assets/55edcf37-97f2-4f0d-b557-c24a8ab1cf0a" />


Then a dialog box will appear for you to save the jpg image to your desktop. 

### HTTP/3 and QUIC 

HTTP/3 also exists in this packet capture in decrypted form. To filter for HTTP/3 traffic use the filter http3. You will never see HTTP/3 content unless you are decrypting your traffic. 


![Screenshot 2025-02-05 113953](https://github.com/user-attachments/assets/6eda6e33-3efb-405c-be90-ffd26b1c0af6)

Stream identifiers are not in parenthesis ()  rather than [] square brackets. 
QUIC also uses a randomly selected Source and Destination Connection ID which will showin the Info column as the DCID number 
Wireshark is currently unable to decode the payload of HTTP/3 "QPACK" compression algorithm, so we are unable to understand what it means, we can see frame types. 

#### QUIC protocol
HTTP/3 traffic is wrapped inside packets utilizing QUIC protocol. QUIC is secure multiplexed data trnsport algorithm that utilizes UDP for the transport layer. It is able to tunnel any protocol and HTTP/3 is one protocol that uses it. 
To find QUIC traffic: look for UDP port 443 by default. 

