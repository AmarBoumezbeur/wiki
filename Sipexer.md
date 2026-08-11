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

## REGISTER

1. Simple REGISTER
```text
sipexer -laddr :5091 -register -vl 2 -co -com -ex 60 -fuser 0990000335832 -fdomain sip-preprod.openvno.net -fv 'touser:phone' -cb -ap "initsys" udp:141.101.55.19:5060
```
2. REGISTER with fixed port, contactparams, call-id and a custom X-NAT header.
```text
sipexer -laddr :5091 -register -vl 2 -co -com -ex 60 -fuser 0990000335832 -fdomain sip-preprod.openvno.net -fv 'touser:phone' -cb -ap "initsys" -fv 'contactparams:audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"' -xh "X-NAT: NAT." -fv 'callid:fixed-test-callid-12345@test'  udp:141.101.55.19:5060
```

## OPTION
1. Simple OPTION
```text
sipexer -vl 2 -co -com -fuser 0990000335832 -fdomain sip-preprod.openvno.net -cb -ap "initsys" udp:141.101.55.19:5060
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
sipexer -info -sd -su udp:141.101.55.19:5060


sipexer -m NOTIFY -sd -su udp:server.com:5060
sipexer -m UPDATE -sd -su udp:server.com:5060
sipexer -m PRACK -sd -su udp:server.com:5060
sipexer -m CANCEL -sd -su udp:server.com:5060
sipexer -m PUBLISH -sd -su udp:server.com:5060
sipexer -m REFER -sd -su udp:server.com:5060