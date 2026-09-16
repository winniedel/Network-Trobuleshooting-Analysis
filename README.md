# Network-Troubleshooting-Analysis

## Project Goal

The purpose of this project was to use Wireshark to analyze a successful and unsuccessful network connection, in a controlled environment using python and Wireshark. It focused on identifying the TCP three-way handshake, analyzing TCP flags and port numbers, observing HTTP application data, and using packet-level evidence to troubleshot why a connection failed.

## Lab Environment

The tools used to complete this project included:
- Wireshark
- Firefox Web Browser
- Python
- TCP/IP
- HTTP
- Ubuntu virtual machine

The python web server ran within the Ubuntu virtual machine and was set to listen to port 8080. Firefox was then used as the client to connect to the local server and through the loopback address 121.0.0.1.

## Successful Connection

Within the this connection, a TCP three-way handshake was successfully established before HTTP application-level communication began. In response, HTTP application data was exchanged from the server.

### Figure 1: Successful TCP Handshake

<img width="1433" height="90" alt="Successful TCP Handshake" src="https://github.com/user-attachments/assets/a5a5d550-e80e-4316-8716-7a5a50ce8729" />

### Figure 2: HTTP Application Traffic

<img width="1430" height="101" alt="HTTP Application Traffic" src="https://github.com/user-attachments/assets/3998a7e9-532b-445c-ac9b-2dc4d67a2fa8" />



## Failed Connection

In this connection, the Python HTTP web server was stopped to see how it would affect the current connection. The client sent a SYN packet to 127.0.0.1 on port 8080 and received a TCP RST/ACK packet instead of SYN/ACK. As a result the TCP connection was not established. Within this connection, a TCP RST packet was also observed as a result of the Python server being stopped before the connection was attempted. Because of this, the connection could not be established.

### Figure 3: Python Server

<img width="576" height="203" alt="Python Server" src="https://github.com/user-attachments/assets/ca6ed891-5ce0-4968-af73-1a54576d1d40" />

### Figure 4: Failed Connection; SYN —> RST/ACK

<img width="1434" height="103" alt="Failed Connection: SYN —  RST:ACK" src="https://github.com/user-attachments/assets/82232024-bdd0-4177-aeb3-7e574dc222f0" />

### Figure 5: RST Filter

<img width="1435" height="429" alt="RST Filter" src="https://github.com/user-attachments/assets/75a59a6b-d942-40b5-9ca3-1331e1b8508d" />




## Troubleshooting Analysis

By using the filter tcp.port == 8080 to isolate traffic associated with the server port, I identified the SYN,SYN/ACK and ACK packets and observed HTTP application data after the handshake. In comparison, the failed connection had multiple SYN connection attempts but I did not observe the SYN/ACK and ACK required to complete the handshake. Instead, RST/ACK packets were observed. The connection failed because the connection to the Python server listening to port 8080 was ended. After the server was stopped, Firefox could no longer establish a TCP connection to port 8080.

## Key Findings 

- When the Python server was running on port 8080, Firefox was able to establish a TCP connection to 127.0.0.1:8080 and exchange HTTP data
- When the server was stopped, Firefox was no longer able to establish a TCP connection to port 8080
- The successful connection completed the TCP three-way handshake, while the failed connection did not
- The failed connection produced TCP RST/ACK packets instead of completing a normal handshake
- No HTTP application data was exchanged during the failed connection because the TCP connection was never successfully established

## Wireshark Filters

- tcp.port == 8080
- tcp.flags.reset == 1
- tcp.analysis.flags
- tcp.analysis.retransmission
  
