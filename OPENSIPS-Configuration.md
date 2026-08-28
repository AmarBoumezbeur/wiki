# Configuration

## Pre-requisites
### Create a domain
1. Configuration dans opensips.cfg
```text
loadmodule "domain.so"
modparam("domain", "db_url", "mysql://opensips:opensips_rw@localhost/opensips")
modparam("domain", "db_mode", 1)   # 0 = cache only, 1 = use DB
```
2. Check opensips syntax and restart it
```text
opensips -C
systemctl restart opensips
```
3. Add route logic to validate domain
```text
route {
    # Reject requests for domains we don't handle
    if (!is_domain_local("$rd")) {
        sl_send_reply("404", "Not Here");
        exit;
    }

    if (is_method("REGISTER")) {
        if (!www_authorize("$td", "subscriber")) {
            www_challenge("$td", "0");
            exit;
        }
        if (!save("location")) {
            sl_reply_error();
        }
        exit;
    }

    # ... rest of routing ...
}
```

## Users
### OpenSips
1. The module responsible for user authentication/authorization:
    - Server side
```text
#### Digest authentication against the database
loadmodule "auth.so"
loadmodule "auth_db.so"
modparam("auth_db", "db_url", "postgres://opensips:9PNTuUGKVs5DPCZ6@localhost/opensips")
modparam("auth_db", "calculate_ha1", 1)
```
    - Client
```text
#### UAC
loadmodule "uac_auth.so"
loadmodule "uac.so"
# UAC restore mode is set to auto for easier From transformation between SIP and TEL URIs
modparam("uac", "restore_mode", "auto")
```
2. The database needs to be created if not alrady created

## Add users
1. Create a user
```text
opensips-cli -x user add username=amar password=AKzlahqscnjdsbf
```
2. Enter the domain when asked
```text
amar.opensips.net
```

## REGISTER
1. Sending the REGISTER to test 
    * Case 1:
```text
sipexer -laddr :5090 -register -vl 3 -co -com -ex 60 -fuser amar -fdomain amar.opensips.net -cb -ap "AKzlahqscnjdsbf" udp:178.170.25.243:5060
```
    * Case 2:
```text
sipexer -laddr :5090 -register -vl 3 -co -com -ex 60 -fuser 33123456789 -fdomain amar.opensips.net -cb -ap "AKzlahqscnjdsbf" udp:178.170.25.243:5060
```
2. Call flow
```text
sipexer -laddr :5090 -register -vl 3 -co -com -ex 60 -fuser amar -fdomain amar.opensips.net -cb -ap "AKzlahqscnjdsbf" udp:178.170.25.243:5060

[debug] [sipexer.go:848] main.main(): parsed socket address argument ({Val:udp:178.170.25.243:5060 Proto:udp Addr:178.170.25.243 Port:5060 PortNo:5060 AType:4 ProtoId:1})
[info] [sipexer.go:2429] main.SIPExerDialogLoop(): local socket address: 192.168.1.12:5090 (udp)
[info] [sipexer.go:2430] main.SIPExerDialogLoop(): local via address: 192.168.1.12:5090
[info] [sipexer.go:2431] main.SIPExerDialogLoop(): sending to udp 178.170.25.243:5060: [[---

REGISTER sip:178.170.25.243:5060 SIP/2.0
Via: SIP/2.0/UDP 192.168.1.12:5090;rport;branch=z9hG4bKSG.wLW2yziKTt2PEWX99C7c91sg
From: <sip:amar@amar.opensips.net>;tag=8ba9da37-bf02-461b-ac9e-db5eae165afe
To: <sip:amar@amar.opensips.net>
Call-ID: 68e1bc8b-8f95-42cd-8921-025444966837
CSeq: 964716 REGISTER
Date: Fri, 28 Aug 2026 23:16:58 CEST
Contact: <sip:192.168.1.12:5090;transport=udp>
Expires: 60
User-Agent: SIPExer v2.0.0
Max-Forwards: 10
Content-Length: 0


[info] [sipexer.go:2433] main.SIPExerDialogLoop(): ---]]

[info] [sipexer.go:2512] main.SIPExerDialogLoop(): message-received: from=178.170.25.243:5060 bytes=472 data=[[---

SIP/2.0 200 OK
Via: SIP/2.0/UDP 192.168.1.12:5090;received=86.217.83.227;rport=5090;branch=z9hG4bKSG.wLW2yziKTt2PEWX99C7c91sg
To: <sip:amar@amar.opensips.net>;tag=5cc0.b5d1806a44abf3e5c5b0ebe6839d13bc
From: <sip:amar@amar.opensips.net>;tag=8ba9da37-bf02-461b-ac9e-db5eae165afe
Call-ID: 68e1bc8b-8f95-42cd-8921-025444966837
CSeq: 964716 REGISTER
Contact: <sip:192.168.1.12:5090;transport=udp>;expires=60
Server: OpenSIPS (3.6.8 (x86_64/linux))
Content-Length: 0


[info] [sipexer.go:2514] main.SIPExerDialogLoop(): ---]]
[debug] [sipexer.go:944] main.SIPExerExit(): return code: 200
```