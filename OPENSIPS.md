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


# Events
## REGISTRAR

Afin de connaître l'état d'enregistrement des UAS/UAC :
OpenSips genére des events losque un REGISTER a été traité et une entrée dans la table location (HSS) a été créée.
Un event est genéré et est capturé par l'application afin de calculer l'état du UAS/UAC.
    
    * Cet état correspond à l'enregistrement ou le non enregistrement, pas à la réception des messages d'enregistrement (REGISTER).
    * Chaque UAS/UAC peut être enregistré plusieurs fois simultanément (jusqu'à 5 fois en théorie), chaque équipement a un état d'enregistrement différent et est différencié des autres par son User-Agent.

On ajoute aussi Contact aux informations de l'équipement, peut aider à différentier plusieurs équipements similaires, notamment avec l'adresse IP, ce champs est affiché tel que reçu.

### Events 
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

### REGISTER

```
U 2026/07/31 10:51:37.315368 217.15.95.98:5060 -> 169.254.74.51:5060
REGISTER sip:sip3.openvno.net:5060 SIP/2.0.
Via: SIP/2.0/UDP 217.15.95.98:5060;branch=z9hG4bKbe63.e74cfa44.0.
Via: SIP/2.0/UDP 141.95.161.166:5060;received=141.95.161.166;branch=z9hG4bK-524287-1---e9072a7a0738f134;rport=5060.
Max-Forwards: 69.
Contact: <sip:0990000415870@141.95.161.166:5060;rinstance=3af294ec9401e21f>.
To: <sip:0990000415870@sip3.openvno.net:5060>.
From: <sip:0990000415870@sip3.openvno.net:5060>;tag=93f19e16.
Call-ID: UOkD5E5R0354For8iu8vLA...
CSeq: 1254 REGISTER.
Expires: 120.
Allow: INVITE, ACK, CANCEL, OPTIONS, BYE, REGISTER, SUBSCRIBE, NOTIFY, REFER, INFO, MESSAGE, UPDATE.
Supported: replaces, timer.
User-Agent: 3CXPhoneSystem 20.0.9.995 (995).
Authorization: Digest username="0990000415870",realm="sip3.openvno.net",nonce="6a6c6237000158b8f8f74aa0a366ec9cdb71cdc5b47ad529",uri="sip:sip3.openvno.net:5060",response="a2eb3a5abf49d554a41c6129b41ec7d2",algori
thm=MD5.
Content-Length: 0.
X-NAT: .
```

### DATA

* Data
* User Agent
* Contact
* NAT: détecté ou non
* Date d'évènement
* Date prévue d'expiration
* État UP ou DOWN
* Call-ID
* Received (ip et port de l'équipement) (vide si REGISTER tranféré, doit être consolidé en base de donnée pour les UP et null sinon).
* Protocol (ajout du 28/02/2019),

FYI: username et contact forment une contrainte d'unicité pour un équipement.
FYI: username, contact et callid forment une contrainte d'unicité pour un enregistrement donné (normalement domain fait aussi partie de la contrainte mais n'est pas présent).

Les données sont oubliées une fois envoyées
## PROBING
