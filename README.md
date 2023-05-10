# WriteUp-challenges-cm9vdC1tZQo
root-me.org

A write up for challenges game for SYS1.

> **Warning**
> Passwords are censored by **** not to be revealed

## CHALLENGES / NETWORK

### FTP - authentication
5 Points

Packet capture analysis
An authenticated file exchange achieved through FTP. Recover the password used by the user.
```sh
andy@pc:~/pcap-challenges$ tshark -r ch1.pcap | grep 'PASS'
   11   7.639420 10.20.144.150 → 10.20.144.151 FTP 81 Request: PASS ********
```
> **Note**
> PASS FTP command 
> 
> A string specifying the user's password follows the PASS command. This command must be immediately preceded by the user name command and, for most sites, completes the user's identification for access control. The password provided with the command is passed in clear text unless a security mechanism (e.g SSL or TLS) has been negotiated prior to the command being issued or an alternative means of providing the password is used (e.g. MD5 S/KEY).

### TELNET - authentication

5 Points

Packet capture analysis
Find the user password in this TELNET session capture.

> Analyze → Follow → TCP Stream

```sh
........... ..!.."..'.....#..%..%........... ..!..".."........P. ....".....b........b....	B.
........................"......'.....#..&..&..$..&..&..$.. .....#.....'........... .9600,9600....#.bam.zing.org:0.0....'..DISPLAY.bam.zing.org:0.0......xterm-color.............!.............."............
OpenBSD/i386 (oof) (ttyp1)

login: .."........"ffaakkee
.
Password:****
.
Last login: Thu Dec  2 21:32:59 on ttyp1 from bam.zing.org
Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF) #4: Tue Oct 12 20:42:32 CDT 1999

Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) 
```

### ETHERNET - frame

10 Points

Frame analysis

Find the (supposed to be) confidential data in this ethernet frame.

The file : ch12.txt
```sh
andy@pc:~/pcap$ cat ch12.txt
00 05 73 a0 00 00 e0 69 95 d8 5a 13 86 dd 60 00
00 00 00 9b 06 40 26 07 53 00 00 60 2a bc 00 00
00 00 ba de c0 de 20 01 41 d0 00 02 42 33 00 00
00 00 00 00 00 04 96 74 00 50 bc ea 7d b8 00 c1
d7 03 80 18 00 e1 cf a0 00 00 01 01 08 0a 09 3e
69 b9 17 a1 7e d3 47 45 54 20 2f 20 48 54 54 50
2f 31 2e 31 0d 0a 41 75 74 68 6f 72 69 7a 61 74
69 6f 6e 3a 20 42 61 73 69 63 20 59 32 39 75 5a
6d 6b 36 5a 47 56 75 64 47 6c 68 62 41 3d 3d 0d
0a 55 73 65 72 2d 41 67 65 6e 74 3a 20 49 6e 73
61 6e 65 42 72 6f 77 73 65 72 0d 0a 48 6f 73 74
3a 20 77 77 77 2e 6d 79 69 70 76 36 2e 6f 72 67
0d 0a 41 63 63 65 70 74 3a 20 2a 2f 2a 0d 0a 0d
0a

andy@pc:~/pcap$ cat ch12.txt | xxd -p -r
s��i��Z��`�@&S`*����� A�B3�tP��}�����Ϡ
	>i��~�GET / HTTP/1.1
Authorization: Basic Y*************A==
User-Agent: InsaneBrowser
Host: www.myipv6.org
Accept: */*

```
> **Note**
> Base64 is recognized by equals (=) at the end of the characters

```sh
andy@pc:~/Downloads/pcap$ echo 'Y*************A==' | base64 --decode 
*****:*****
```

### Twitter authentication

15 Points

Packet capture analysis

A twitter authentication session has been captured, you have to retrieve the password.

```sh
Open .pcap in Wireshark.

Right click, select: Follow TCP stream.

...
Authorization: Basic d****************=
...

andy@pc:~$ echo  d****************= | base64 --decode

********:********
```

### Bluetooth - Unknown file

15 Points

Packet capture analysis

Your friend working at NSA recovered an unreadable file from a hacker’s computer. The only thing he knows is that it comes from a communication between a computer and a phone.

The answer is the sha-1 hash of the concatenation of the MAC address (uppercase) and the name of the phone.

Example:
AB:CD:EF:12:34:56myPhone -> ****************************************

```sh
andy@pc:~$ file ch18.bin
ch18.bin: BTSnoop version 1, HCI UART (H4)

andy@pc:~$ sudo wireshark ch18.bin 

(In the toolbar)Wireless > Bluetooth Devices
0c:b3:19:b9:4f:c6 SamsungE GT-S7390G

Uppercase the MAC Address

andy@pc:~$ echo -n 0C:B3:19:B9:4F:C6GT-S7390G | sha1sum
****************************************
```

### CISCO - password

15 Points

It’s not always a hash.

Find the "Enable" password.

```sh 
andy@pc:~$ cat ch15.txt 
|
username hub password 7 025017705B3907344E
username admin privilege 15 password 7 10181A325528130F010D24
username guest password 7 124F163C42340B112F3830
password 7 144101205C3B29242A3B3C3927
enable secret 5 $1$p8Y6$MCdRLBzuGlfOs9S.hXOp0.
|

google-chrome "http://www.ifm.net.nz/cookbooks/passwordcracker.html"

And we get

username hub password 7 
025017705B3907344E = 6sK0_hub

username admin privilege 15 password 7 
10181A325528130F010D24 = 6sK0_admin

username guest password 7 
124F163C42340B112F3830 = 6sK0_guest

password 7 
144101205C3B29242A3B3C3927 = 6sK0_console

enable secret 5 
$1$p8Y6$MCdRLBzuGlfOs9S.hXOp0. = 6sK0_??????

Use your reasoning xD
Or a bruteforce with english word dictionnary

6sK0_******
```

> **Note**
> Cisco type 7 is crackable 
> but type 5 is not crackable, so use a bruteforce with a dictionary
> or a reasoning (in some cases)

> **Tip**
> Human-chosen Passwords
> https://csis.gmu.edu/ksun/publications/Password-infocom2016.pdf 
