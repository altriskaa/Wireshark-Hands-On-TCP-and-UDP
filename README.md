# Hands On Wireshark TCP and UDP

## TCP
### 1. Capturing a bulk TCP transfer from your computer to a remote server
- Buka web browser (di sini saya memakai Google Chrome) dan buka http://gaia.cs.umass.edu/wireshark-labs/alice.txt
- Download `alice.txt` dengan `Ctrl+S`
- Buka http://gaia.cs.umass.edu/wireshark-labs/TCP-wireshark-file1.html dan upload `alice.txt`
- Buka Wireshark dan pilih `Ethernet`
- Kembali lagi ke web browser, klik button `Upload alice.txt file`
- Setelah berhasil di-upload, kembali ke Wireshark dan klik `Stop capturing packets`
- Sekarang kita punya packet trace yang akan digunakan untuk soal-soal berikutnya
### 2. A first look at the captured trace
- Lakukan filter `tcp` sehingga didapatkan packet-packet sebagai berikut:
  
![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/37874556-e02c-433e-8ef4-7bb59e17b3a3)

> 1. What is the IP address and TCP port number used by the client computer (source) that is transferring the alice.txt file to gaia.cs.umass.edu? To answer this question, it’s probably easiest to select an HTTP message and explore the details of the TCP packet used to carry this HTTP message, using the “details of the selected packet header window” (refer to Figure 2 in the “Getting Started with Wireshark” Lab if you’re uncertain about the Wireshark windows).

#### Solution
- Didapatkan **IP address** dan **TCP port number** yang digunakan oleh source
  
![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/0becf080-d0b4-4c94-82dd-3ba0f45d815d)

>2. What is the IP address of gaia.cs.umass.edu? On what port number is it sending and receiving TCP segments for this connection?

#### Solution
- Didapatkan **IP address** dan **TCP port number** gaia.cs.umass.edu
  
![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/92fca3ef-d540-4e52-95e4-43f19e0c3288)

### 3. TCP Basics
> 3. What is the sequence number of the TCP SYN segment that is used to initiate the TCP connection between the client computer and gaia.cs.umass.edu? (Note: this is the “raw” sequence number carried in the TCP segment itself; it is NOT the packet # in the “No.” column in the Wireshark window. Remember there is no such thing as a “packet number” in TCP or UDP; as you know, there are sequence numbers in TCP and that’s what we’re after here. Also note that this is not the relative sequence number with respect to the starting sequence number of this TCP session.). What is it in this TCP segment that identifies the segment as a SYN segment? Will the TCP receiver in this session be able to use Selective Acknowledgments (allowing TCP to function a bit more like a “selective repeat” receiver, see section 3.4.5 in the text)?

- Pertama-tama saya lakukan sort berdasarkan waktu
  
![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/54671800-0a34-4e5e-abdc-7b42574fa7a0)

- Cari packet `SYN` pertama sehingga didapatkan raw sequence number **1997711021** dan IP address **192.168.1.13**
- Dalam flag `0x002 (SYN)` ditunjukkan bahwa `Syn: Set`, maka dapat dipastikan itu adalah **SYN**

> 4. What is the sequence number of the SYNACK segment sent by gaia.cs.umass.edu to the client computer in reply to the SYN? What is it in the segment that identifies the segment as a SYNACK segment? What is the value of the Acknowledgement field in the SYNACK segment? How did gaia.cs.umass.edu determine that value? 

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/673b2892-d9a1-4315-b537-799478ff9b20)

- SYN ACK raw sequence number: **2251369845**
- Raw acknowledge number: **1997711022**
- Dapat dilihat **raw sequence number** ACK segment setelah SYN ACK segment memiliki persamaan dengan **raw acknowledge number** SYN ACK segment, hal ini menjadi identifier SYN ACK segment tersebut

> 5. What is the sequence number of the TCP segment containing the header of the HTTP POST command? Note that in order to find the POST message header, you’ll need to dig into the packet content field at the bottom of the Wireshark window, looking for a segment with the ASCII text “POST” within its DATA field 4,5. How many bytes of data are contained in the payload (data) field of this TCP segment? Did all of the data in the transferred file alice.txt fit into this single segment?
- Temukan `POST`
  
![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/ae88614d-99ba-4f01-a7a5-5a3c2d20ff35)

- Raw sequence number: **2251369846**
- TCP Payload: **633 bytes**


> 6. Consider the TCP segment containing the HTTP “POST” as the first segment in the data transfer part of the TCP connection.
> - At what time was the first segment (the one containing the HTTP POST) in the data-transfer part of the TCP connection sent?

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/7655be3e-288d-4e58-8eda-742060d6ea9c)

**Answer:** 2.913655

> - At what time was the ACK for this first data-containing segment received?

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/0d6a66a3-34a7-49df-95dd-dd7fbff63f83)

**Answer:** 2.913860

> - What is the RTT for this first data-containing segment?

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/e17d6288-ae7d-4a10-8de9-ae9b1df54c7d)

**Answer:** 0.001763000
> - What is the RTT value the second data-carrying TCP segment and its ACK?

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/fb4d2422-d9a6-476f-b39a-e9c88a87c2a4)

**Answer:** 0.001763000

<!-- **NOTICE: Pada nomor berikut ini saya akan menggunakan trace packet yang disediakan dari soal (tcp-wireshark-trace1-1.pcapng)** -->

> 7. What is the length (header plus payload) of each of the first four data-carrying TCP segments?
- Header: 32
- Payload: 633 bytes
- Header + Payload = 32 + 633
- **Total Length: 665 bytes**
> 8. What is the minimum amount of available buffer space advertised to the client by gaia.cs.umass.edu among these first four data-carrying TCP segments? Does the lack of receiver buffer space ever throttle the sender for these first four datacarrying segments?

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/b348f3d6-8f9e-48bb-aa56-1bc5fc60f480)

- Minimum amount: **64240**
- **Tidak**, karena segment length lebih kecil dari window size

> 9. Are there any retransmitted segments in the trace file? What did you check for (in the trace) in order to answer this question?
- **Tidak**, dalam trace packet saya, setiap sequence number dari setiap segment selalu meningkat sehingga dapat dipastikan bahwa **tidak ada** retransmit
> 10. How much data does the receiver typically acknowledge in an ACK among the first ten data-carrying segments sent from the client to gaia.cs.umass.edu? Can you identify cases where the receiver is ACKing every other received segment (see Table 3.2 in the text) among these first ten data-carrying segments?
- **622 bytes**
> 11. What is the throughput (bytes transferred per unit time) for the TCP connection? Explain how you calculated this value.

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/36db15f8-dc09-4103-aefe-56ebec77733d)

- TCP segment pertama adalah **1 bytes** karena `Ack=1` dan transmission time-nya **2.913655 seconds**
- Kemudian cari segment `HTTP OK` dan lihat packet di atasnya untuk mendapatkan segment terakhir

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/fe41fdb2-f1bc-4ed3-bccd-081c6cb72b7b)

- Ditemukan bahwa segment terakhir memiliki **152955 bytes** dan transmission time-nya **4.028852 seconds**
- Kalkulasinya adalah sebagai berikut
```
Total data
152955 - 1 = 152954 bytes

Selisih transmission time
4.028852 - 2.913655 = 1.115197 seconds

Throughput
(152954 / 1.115197) * 8 bits/second = 137154.2427033071 MB/s
```

### 4. TCP congestion control in action
- Buka `Statistics`
- Pada `TCP Stream Graphs`, buka `Time Sequence (Stevens)`
- Klik `Switch Directions`
  
![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/380c4438-567d-4540-a9f5-393f9eca3c1f)

## UDP

> 1. Select the first UDP segment in your trace. What is the packet number of this segment in the trace file? What type of application-layer payload or protocol message is being carried in this UDP segment? Look at the details of this packet in Wireshark. How many fields there are in the UDP header? (You shouldn’t look in the textbook! Answer these questions directly from what you observe in the packet trace.) What are the names of these fields? 

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/756a810d-8173-4b41-aef5-e858bb7bb2ba)

- Filter packets dengan `udp`
- Klik salah satu packet dan lihat headernya
- Terdapat **4 fields** di header yaitu `Source port`, `Destination port`, `Length`, and `Checksum`

> 2. By consulting the displayed information in Wireshark’s packet content field for this packet (or by consulting the textbook), what is the length (in bytes) of each of the UDP header fields?

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/6c89d653-e6f8-4244-891f-1a38be54004d)

- Setiap header field memiliki length sebesar **2 bytes**

> 3. The value in the Length field is the length of what? (You can consult the text for this answer). Verify your claim with your captured UDP packet.
- **Sum** dari **8 header bytes**

> 4. What is the maximum number of bytes that can be included in a UDP payload? (Hint: the answer to this question can be determined by your answer to 2. above)
- Source port number paling besar adalah (2^16-1) = **65535**
- Header bytes adalah **8 bytes**
- Jadi, bytes maksimum yang bisa di UDP payload adalah `65535 - 8 = 65527 bytes`

> 5. What is the largest possible source port number? (Hint: see the hint in 4.)
- Source port number paling besar adalah (2^16-1) = **65535**

> 6. What is the protocol number for UDP? Give your answer in decimal notation. To answer this question, you’ll need to look into the Protocol field of the IP datagram containing this UDP segment (see Figure 4.13 in the text, and the discussion of IP header fields).

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/1f28eea1-5b5c-44fa-9a78-8d403e9b9af9)

- Decimal = **17**
- Hexadecimal = **11**

> 7. Examine the pair of UDP packets in which your host sends the first UDP packet and the second UDP packet is a reply to this first UDP packet. (Hint: for a second packet to be sent in response to a first packet, the sender of the first packet should be the destination of the second packet). What is the packet number of the first of these two UDP segments in the trace file? What is the packet number of the second of these two UDP segments in the trace file? Describe the relationshipbetween the port numbers in the two packets.

![image](https://github.com/altriskaa/Wireshark-Hands-On-TCP-and-UDP/assets/114663340/a89f01ec-bea1-4291-a069-32443478e0c2)

- Packet no 248 adalah *send packet*
- Packet no 250 adalah *reply packet*
```
send packet source port = reply packet destination port
reply packet destination port = send packet source port
```
