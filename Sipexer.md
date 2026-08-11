# Introduction 

Sipexer is a modern and fast SIP CLI tool.

[Sipexer](https://github.com/miconda/sipexer)

# Installation

```text
# Check https://github.com/miconda/sipexer/releases for the latest version/asset name

cd /tmp

wget https://github.com/miconda/sipexer/releases/download/v1.1.0/sipexer-v1.1.0-linux-amd64.tar.gz

tar -xzf sipexer-v1.1.0-linux-amd64.tar.gz

sudo mv sipexer /usr/local/bin/

sudo chmod +x /usr/local/bin/sipexer

sipexer -h
```

# SIP METHODS
## INVITE
1. Simple INVITE (407 Proxy Authentication Required)
```text
sipexer -invite -vl 3 -co -com -fuser 0990000360610 -tuser +33752025092 -fdomain sip-preprod.openvno.net -cb -ap "qdf6qs435d1f2q" -sd -su udp:141.101.55.19:5060
```
    * 100 Giving a try
```
[debug] [sipexer.go:848] main.main(): parsed socket address argument ({Val:udp:141.101.55.19:5060 Proto:udp Addr:141.101.55.19 Port:5060 PortNo:5060 AType:4 ProtoId:1})
[info] [sipexer.go:2429] main.SIPExerDialogLoop(): local socket address: 192.168.99.20:56259 (udp)
[info] [sipexer.go:2430] main.SIPExerDialogLoop(): local via address: 192.168.99.20:56259
[info] [sipexer.go:2431] main.SIPExerDialogLoop(): sending to udp 141.101.55.19:5060: [[---

INVITE sip:+33752025092@141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:56259;rport;branch=z9hG4bKSG.MCMz78C2Qrydhuun4T8bjA
From: <sip:0990000360610@141.101.55.19>;tag=adba6c46-d245-4009-a48f-090082ef2bb6
To: <sip:+33752025092@141.101.55.19>
Call-ID: 1605a974-d1a2-41b5-ae09-e6c1c0f08f2d
CSeq: 552269 INVITE
Date: Tue, 11 Aug 2026 20:22:42 CEST
Contact: <sip:192.168.99.20:56259;transport=udp>
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 220
Content-Type: application/sdp

v=0
o=sipexer 1786472562 1786472562 IN IP4 192.168.99.20
s=call
c=IN IP4 192.168.99.20
t=0 0
m=audio 27749 RTP/AVP 0 8 101
a=rtpmap:0 pcmu/8000
a=rtpmap:8 pcma/8000
a=rtpmap:101 telephone-event/8000
a=sendrecv

[info] [sipexer.go:2433] main.SIPExerDialogLoop(): ---]]

[info] [sipexer.go:2512] main.SIPExerDialogLoop(): message-received: from=141.101.55.19:5060 bytes=524 data=[[---

SIP/2.0 407 Proxy Authentication Required
Via: SIP/2.0/UDP 192.168.99.20:56259;received=217.15.84.94;rport=56259;branch=z9hG4bKSG.MCMz78C2Qrydhuun4T8bjA
From: <sip:0990000360610@141.101.55.19>;tag=adba6c46-d245-4009-a48f-090082ef2bb6
To: <sip:+33752025092@141.101.55.19>;tag=124e.df837575f45aaa00412199a91090cce9
Call-ID: 1605a974-d1a2-41b5-ae09-e6c1c0f08f2d
CSeq: 552269 INVITE
Proxy-Authenticate: Digest realm="sip-preprod.openvno.net", nonce="6a7b68900000982478c67be0217607c10ba6034ea2d273a0"
Content-Length: 0


[info] [sipexer.go:2514] main.SIPExerDialogLoop(): ---]]
[debug] [sipexer.go:2100] main.SIPExerProcessResponse(): 
Auth params map:
    map[body:v=0
o=sipexer 1786472562 1786472562 IN IP4 192.168.99.20
s=call
c=IN IP4 192.168.99.20
t=0 0
m=audio 27749 RTP/AVP 0 8 101
a=rtpmap:0 pcmu/8000
a=rtpmap:8 pcma/8000
a=rtpmap:101 telephone-event/8000
a=sendrecv
 method:INVITE nonce:6a7b68900000982478c67be0217607c10ba6034ea2d273a0 realm:sip-preprod.openvno.net uri:sip:+33752025092@141.101.55.19:5060]

[debug] [sipexer.go:2143] main.SIPExerProcessResponse(): authentication header body: [[Digest username="0990000360610", realm="sip-preprod.openvno.net", nonce="6a7b68900000982478c67be0217607c10ba6034ea2d273a0", uri="sip:+33752025092@141.101.55.19:5060", algorithm=MD5, response="307ce064539e95b225ada6d19d75c6f2"]]
[info] [sipexer.go:2714] main.SIPExerDialogLoop(): sending to udp 141.101.55.19:5060: [[---

ACK sip:+33752025092@141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:56259;rport;branch=z9hG4bKSG.MCMz78C2Qrydhuun4T8bjA
From: <sip:0990000360610@141.101.55.19>;tag=adba6c46-d245-4009-a48f-090082ef2bb6
To: <sip:+33752025092@141.101.55.19>;tag=124e.df837575f45aaa00412199a91090cce9
Call-ID: 1605a974-d1a2-41b5-ae09-e6c1c0f08f2d
CSeq: 552269 ACK
Content-Length: 0


[info] [sipexer.go:2716] main.SIPExerDialogLoop(): ---]]

[info] [sipexer.go:2831] main.SIPExerDialogLoop(): sending to udp 141.101.55.19:5060: [[---

INVITE sip:+33752025092@141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:56259;rport;branch=z9hG4bKSG.7c1bd4e9-1630-4411-a64a-071e90386e5d
From: <sip:0990000360610@141.101.55.19>;tag=adba6c46-d245-4009-a48f-090082ef2bb6
To: <sip:+33752025092@141.101.55.19>
Call-ID: 1605a974-d1a2-41b5-ae09-e6c1c0f08f2d
CSeq: 552270 INVITE
Date: Tue, 11 Aug 2026 20:22:42 CEST
Contact: <sip:192.168.99.20:56259;transport=udp>
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 220
Content-Type: application/sdp
Proxy-Authorization: Digest username="0990000360610", realm="sip-preprod.openvno.net", nonce="6a7b68900000982478c67be0217607c10ba6034ea2d273a0", uri="sip:+33752025092@141.101.55.19:5060", algorithm=MD5, response="307ce064539e95b225ada6d19d75c6f2"

v=0
o=sipexer 1786472562 1786472562 IN IP4 192.168.99.20
s=call
c=IN IP4 192.168.99.20
t=0 0
m=audio 27749 RTP/AVP 0 8 101
a=rtpmap:0 pcmu/8000
a=rtpmap:8 pcma/8000
a=rtpmap:101 telephone-event/8000
a=sendrecv

[info] [sipexer.go:2833] main.SIPExerDialogLoop(): ---]]

[info] [sipexer.go:2512] main.SIPExerDialogLoop(): message-received: from=141.101.55.19:5060 bytes=361 data=[[---

SIP/2.0 100 Giving a try
Via: SIP/2.0/UDP 192.168.99.20:56259;received=217.15.84.94;rport=56259;branch=z9hG4bKSG.7c1bd4e9-1630-4411-a64a-071e90386e5d
From: <sip:0990000360610@141.101.55.19>;tag=adba6c46-d245-4009-a48f-090082ef2bb6
To: <sip:+33752025092@141.101.55.19>
Call-ID: 1605a974-d1a2-41b5-ae09-e6c1c0f08f2d
CSeq: 552270 INVITE
Content-Length: 0
```
2. Simple REGISTER + INVITE
```text
sipexer -laddr :5091 --register-first --invite -vl 3 -co -com -fuser 0990000360610 -tuser +33750752395 -fdomain sip-preprod.openvno.net -ap "qdf6qs435d1f2q" -cb -sd -su udp:141.101.55.19:5060
```

## REGISTER

1. Simple REGISTER
```text
sipexer -laddr :5091 -register -vl 3 -co -com -ex 60 -fuser 0990000335832 -fdomain sip-preprod.openvno.net -cb -ap "initsys" udp:141.101.55.19:5060
```
2. REGISTER with fixed port, contactparams, call-id and a custom X-NAT header.
```text
sipexer -laddr :5091 -register -vl 3 -co -com -ex 60 -fuser 0990000335832 -fdomain sip-preprod.openvno.net -fv 'touser:phone' -cb -ap "initsys" -fv 'contactparams:audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"' -xh "X-NAT: NAT." -fv 'callid:fixed-test-callid-12345@test'  udp:141.101.55.19:5060
```

## OPTION
1. Simple OPTION
```text
sipexer -vl 3 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -cb -ap "initsys" udp:141.101.55.19:5060
```
2. OPTION with ..port and call-id fixed
```text
sipexer -laddr :5091 -vl 2 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -cb -ap "initsys"  -fv 'callid:fixed-test-callid-12345@test' udp:141.101.55.19:5060

```
3. RETURN

    * 403 FORBIDDEN
```text
debug] [sipexer.go:848] main.main(): parsed socket address argument ({Val:udp:141.101.55.19:5060 Proto:udp Addr:141.101.55.19 Port:5060 PortNo:5060 AType:4 ProtoId:1})
[info] [sipexer.go:2429] main.SIPExerDialogLoop(): local socket address: 192.168.1.12:56800 (udp)
[info] [sipexer.go:2430] main.SIPExerDialogLoop(): local via address: 192.168.1.12:56800
[info] [sipexer.go:2431] main.SIPExerDialogLoop(): sending to udp 141.101.55.19:5060: [[---

OPTIONS sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.1.12:56800;rport;branch=z9hG4bKSG.4fljabexRYmJX91kgfk1pXQ
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=e665f181-3b83-4977-88e2-91e176007219
To: <sip:bob@localhost>
Call-ID: 28c28efe-570b-4b64-a1ba-4543220156d7
CSeq: 976067 OPTIONS
Date: Tue, 11 Aug 2026 12:33:43 CEST
Contact: <sip:192.168.1.12:56800;transport=udp>
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0


[info] [sipexer.go:2433] main.SIPExerDialogLoop(): ---]]

[info] [sipexer.go:2512] main.SIPExerDialogLoop(): message-received: from=141.101.55.19:5060 bytes=385 data=[[---

SIP/2.0 403 Forbidden
Via: SIP/2.0/UDP 192.168.1.12:56800;received=86.217.83.227;rport=56800;branch=z9hG4bKSG.4fljabexRYmJX91kgfk1pXQ
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=e665f181-3b83-4977-88e2-91e176007219
To: <sip:bob@localhost>;tag=124e.b89061158cfa548c53145e4bff15a5d7
Call-ID: 28c28efe-570b-4b64-a1ba-4543220156d7
CSeq: 976067 OPTIONS
Content-Length: 0


[info] [sipexer.go:2514] main.SIPExerDialogLoop(): ---]]
[debug] [sipexer.go:944] main.SIPExerExit(): return code: 403
```
    * 200 ok
```text
[debug] [sipexer.go:848] main.main(): parsed socket address argument ({Val:udp:141.101.55.19:5060 Proto:udp Addr:141.101.55.19 Port:5060 PortNo:5060 AType:4 ProtoId:1})
[info] [sipexer.go:2429] main.SIPExerDialogLoop(): local socket address: 192.168.99.20:34179 (udp)
[info] [sipexer.go:2430] main.SIPExerDialogLoop(): local via address: 192.168.99.20:34179
[info] [sipexer.go:2431] main.SIPExerDialogLoop(): sending to udp 141.101.55.19:5060: [[---

OPTIONS sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:34179;rport;branch=z9hG4bKSG.08TWeb7rToGaMuB91GxCG3Q
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=72d5308f-eb2c-42e9-9b0a-36b923c90802
To: <sip:bob@localhost>
Call-ID: 273fec07-f210-479c-86d0-1abff57d76f3
CSeq: 597539 OPTIONS
Date: Tue, 11 Aug 2026 12:40:50 CEST
Contact: <sip:192.168.99.20:34179;transport=udp>
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0


[info] [sipexer.go:2433] main.SIPExerDialogLoop(): ---]]

[info] [sipexer.go:2512] main.SIPExerDialogLoop(): message-received: from=141.101.55.19:5060 bytes=378 data=[[---

SIP/2.0 200 OK
Via: SIP/2.0/UDP 192.168.99.20:34179;received=217.15.84.94;rport=34179;branch=z9hG4bKSG.08TWeb7rToGaMuB91GxCG3Q
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=72d5308f-eb2c-42e9-9b0a-36b923c90802
To: <sip:bob@localhost>;tag=124e.3f03eb9c4aa293cc9f465be2bd6bf150
Call-ID: 273fec07-f210-479c-86d0-1abff57d76f3
CSeq: 597539 OPTIONS
Content-Length: 0


[info] [sipexer.go:2514] main.SIPExerDialogLoop(): ---]]
[debug] [sipexer.go:944] main.SIPExerExit(): return code: 200
```
## INFO
1. Simple INFO
```text
sipexer -info -vl 3 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -ap "initsys" -cb -sd -su  udp:141.101.55.19:5060
```

## NOTIFY
1. Simple NOTIFY
```text
sipexer -notify -vl 3 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -ap "initsys" -cb -sd -su  udp:141.101.55.19:5060
```
## CANCEL
1. Simple CANCEL
```text
sipexer -cancel -vl 3 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -ap "initsys" -cb -sd -su  udp:141.101.55.19:5060
```

## PUBLISH
1. Simple PUBLISH
```text
sipexer -publish -vl 3 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -ap "initsys" -cb -sd -su  udp:141.101.55.19:5060
```

## NOTIFY
2. Simple NOTIFY
```text
sipexer -notify -vl 3 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -ap "initsys" -cb -sd -su  udp:141.101.55.19:5060
```