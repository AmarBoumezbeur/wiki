# Introduction

This wiki is dedicated to explaining OpenSIPS. (A very fast SIP proxy server).

# References

1. [SIP](http://0.0.0.0:4567/gollum/create/SIP)
2. [OpenSIPS](https://www.opensips.org/)
3. [OpenSIPS Github](https://github.com/OpenSIPS/OpenSIPS)
4. [Build OpenSIPS](http://0.0.0.0:4567/gollum/create/Build-OpenSIPS)

# Architecture

1. Single node mode (Dev only)
2. Cluster mode (Prod)

# Configuration

# Service


***

# UAS/UAC location

## Architecure

## Configuration

## Events

### REGISTRAR

REGISTRAR UAS/UAC is a type of of User-Agents that uses REGISTER method to log its location with a SIP Proxy server.

In order to keep track of UAS/UAC location, OpenSIPS uses a mecanism to log the location of each REGISTER in it's DB/cache/memory. The mecanism is based on events. An event is triggered when an entry in the table location is detected.
The events are stored in OpenSIPS DB as AOR.
```
AOR:: UAS1
        Contact:: sip:UAS1@10.1.1.1;transport=UDP;user=phone Q=
                ContactID:: 564779540772033556
                Expires:: 21
                Callid:: d3d5c9fab3fc13ad0d0c3c5938efb340@10.1.1.1
                Cseq:: 2146524753
                User-agent:: OXO052/035.001 GW_052/035.001
                State:: CS_NEW
                Flags:: 0
                Cflags::
                Socket:: udp:169.254.74.51:5060
                Methods:: 7279
```

#### REGISTER

When a REGISTER is recceived by OpenSIPS, it is challenged with 401/Unauthorized or 407/Proxy Authentication Required, the UAS sends a new REGISTER with Authorization header and this time if the UAS is authorized a 200 OK response is sent as response.

```
REGISTER sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:5091;rport;branch=z9hG4bKSG.JNlswMLATPGbKLBc99BEEow
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>
Call-ID: fixed-test-callid-12345@test
CSeq: 750121 REGISTER
Date: Tue, 04 Aug 2026 15:28:54 CEST
Contact: <sip:192.168.99.20:5091;transport=udp>;audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"
Expires: 60
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0
X-NAT: NAT.

SIP/2.0 401 Unauthorized
Via: SIP/2.0/UDP 192.168.99.20:5091;received=217.15.84.94;rport=5091;branch=z9hG4bKSG.JNlswMLATPGbKLBc99BEEow
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>;tag=124e.0808d8ce1c30ee5f7ed80f6ff7bb8e37
Call-ID: fixed-test-callid-12345@test
CSeq: 750121 REGISTER
WWW-Authenticate: Digest realm="sip-preprod.openvno.net", nonce="6a71e93400000c51fe89aa585bf0d67f3b20d640c749785d"
Content-Length: 0

REGISTER sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:5091;rport;branch=z9hG4bKSG.e370845f-8a84-443e-9f9a-79e252ce225b
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>
Call-ID: fixed-test-callid-12345@test
CSeq: 750122 REGISTER
Date: Tue, 04 Aug 2026 15:28:54 CEST
Contact: <sip:192.168.99.20:5091;transport=udp>;audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"
Expires: 60
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0
X-NAT: NAT.
Authorization: Digest username="0990000335832", realm="sip-preprod.openvno.net", nonce="6a71e93400000c51fe89aa585bf0d67f3b20d640c749785d", uri="sip:141.101.55.19:5060", algorithm=MD5, response="eb12e194e0a4cddd457500f1085ad75a"

SIP/2.0 200 OK
Via: SIP/2.0/UDP 192.168.99.20:5091;received=217.15.84.94;rport=5091;branch=z9hG4bKSG.e370845f-8a84-443e-9f9a-79e252ce225b
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=60ea7afb-4655-4d9f-951a-8f0452e27817
To: <sip:0990000335832@sip-preprod.openvno.net>;tag=6818-49340d08fcc0f1405a89609167823507
Call-ID: fixed-test-callid-12345@test
CSeq: 750122 REGISTER
Contact: <sip:192.168.99.20:5091;transport=udp>;expires=60;received="sip:217.15.84.94:5091"
Content-Length: 0
```

#### Events 

Events are three types: 

1. INSERT
```
U 2025/11/27 10:36:44.636474 127.0.0.1:56349 -> 127.0.0.1:12345
E_UL_CONTACT_INSERT
aor::0990000348419
uri::sip:0990000348419@185.204.188.227:5065;rinstance=3A92D9725EFF4BBA63608EC211A9F55F
received::
path::
qval::18446744073709551615
socket::udp:169.254.74.51:5060
bflags::0
expires::1764236504
callid::Hrtb4QZimtc1Ak_HSKcIrA..
cseq::2
attr::
latency::0
```
2. DELETE
```
U 2025/11/27 10:38:30.989721 127.0.0.1:38121 -> 127.0.0.1:12345
E_UL_CONTACT_DELETE
aor::0990000362888
uri::sip:0990000362888@192.168.1.246;transport=UDP;user=phone
received::
path::
qval::18446744073709551615
socket::udp:169.254.74.51:5060
bflags::0
expires::1764236275
callid::6ff8912301a845aae931fa313d8cb6cc@192.168.1.246
cseq::2147431040
attr::
latency::0
```
3. UPDATE
```
U 2025/11/27 10:38:30.989721 127.0.0.1:38121 -> 127.0.0.1:12345
E_UL_CONTACT_UPDATE
aor::0990000362888
uri::sip:0990000362888@192.168.1.246;transport=UDP;user=phone
received::
path::
qval::18446744073709551615
socket::udp:169.254.74.51:5060
bflags::0
expires::1764236275
callid::6ff8912301a845aae931fa313d8cb6cc@192.168.1.246
cseq::2147431040
attr::
latency::0
```

### PROBING

PROBING UAS/UAC are types of User-Agents that uses OPTION METHOD to lof their location with a SIP proxy Server.
OpenSIPS 

#### OPTION

```
OPTIONS sip:141.101.55.19:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.99.20:5091;rport;branch=z9hG4bKSG.WS90vkc7uTSus7wvfLgW0EQ
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=516aa4bb-b7d2-456a-91e2-b6d817899261
To: <sip:bob@localhost>
Call-ID: fixed-test-callid-12345@test
CSeq: 396727 OPTIONS
Date: Tue, 04 Aug 2026 15:34:00 CEST
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0
X-NAT: NAT.

SIP/2.0 200 OK
Via: SIP/2.0/UDP 192.168.99.20:5091;received=217.15.84.94;rport=5091;branch=z9hG4bKSG.WS90vkc7uTSus7wvfLgW0EQ
From: <sip:0990000335832@sip-preprod.openvno.net>;tag=516aa4bb-b7d2-456a-91e2-b6d817899261
To: <sip:bob@localhost>;tag=124e.621cb9786b251e3fa76e8b072389bad6
Call-ID: fixed-test-callid-12345@test
CSeq: 396727 OPTIONS
Content-Length: 0

```

#### Events

* STATUS
```
U 2025/12/11 12:07:37.565509 127.0.0.1:37118 -> 127.0.0.1:12345
E_DROUTING_STATUS
partition::Default
gwid::3373
address::sip.pstnhub.microsoft.com:5061;transport=tls
status::inactive
```

## Tools
### Sipexer
#### REGISTER

> sipexer -laddr :5091 -register -vl 2 -co -com -ex 60 -fuser 0990000335832 -fdomain sip-preprod.openvno.net -fv 'touser:phone' -cb -ap "initsys" -fv 'contactparams:audio;class="business, personal";duplex="full, half";mobility="fixed";description="<OmniPCX Office>";methods="ACK, INVITE, CANCEL, BYE, REFER, NOTIFY, OPTIONS, PRACK, UPDATE";extensions="100rel, timer, from-change";schemes="sip"' -xh "X-NAT: NAT." -fv 'callid:fixed-test-callid-12345@test'  udp:141.101.55.19:5060


#### OPTIONS

> sipexer -laddr :5091 -vl 2 -co -com   -fuser 0990000335832 -fdomain sip-preprod.openvno.net   -ap "initsys"   -xh "X-NAT: NAT."   -fv 'callid:fixed-test-callid-12345@test'   udp:141.101.55.19:5060
