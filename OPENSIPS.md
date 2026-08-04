# Introduction

This wiki is dedicated to explaining OpenSIPS. (A very fast SIP proxy server).

# References

1. [SIP](http://0.0.0.0:4567/gollum/create/SIP)
2. [OpenSIPS](https://www.opensips.org/)
3. [OpenSIPS Github](https://github.com/OpenSIPS/OpenSIPS)
4. [Build OpenSIPS](http://0.0.0.0:4567/gollum/create/Build-OpenSIPS)


***
# PCSCF


# Architecture

1. Single node mode (Dev only)
2. Cluster mode (Prod)

# Configuration

# Service


***

# UAS/UAC location

## Architecure

The architecture is pretty unique since an additional OpenSIPS server needs to be added in the environment in prder to receive REGISTER/OPTIONS messages forwarded by the OpenSIPS servers that actually receive them and reply to them woth the proper response messages.



## Configuration
1. /etc/opensips/opensips.cfg
```
####### Global Parameters #########

log_level=2
log_stderror=no
log_facility=LOG_LOCAL0

children=4

/* uncomment the next line to enable the auto temporary blacklisting of 
   not available destinations (default disabled) */
#disable_dns_blacklist=no

/* uncomment the next line to enable IPv6 lookup after IPv4 dns 
   lookup failures (default disabled) */
#dns_try_ipv6=yes

/* comment the next line to enable the auto discovery of local aliases
   based on revers DNS on IPs */
auto_aliases=no

server_signature=no # FFT disallow Server header

#set module path
mpath="/usr/lib/x86_64-linux-gnu/opensips/modules"

loadmodule "proto_udp.so"

listen=udp:172.16.5.72:5060

####### Modules Section ########

### Amar - Adding event modules
#loadmodule "event.so"
#loadmodule "event_flatstore.so"   # or kafka / rabbit / REST / etc

#### SIGNALING module
loadmodule "signaling.so"

#### StateLess module
loadmodule "sl.so"

#### Transaction Module
loadmodule "tm.so"
modparam("tm", "fr_timeout", 12)
modparam("tm", "fr_inv_timeout", 200)
modparam("tm", "T1_timer", 250)
modparam("tm", "wt_timer", 12)
modparam("tm", "restart_fr_on_each_reply", 1)
modparam("tm", "onreply_avp_mode", 1)

#### MAX ForWarD module
loadmodule "maxfwd.so"

#### SIP MSG OPerationS module
loadmodule "sipmsgops.so"

#### FIFO Management Interface
loadmodule "mi_fifo.so"
modparam("mi_fifo", "fifo_name", "/var/run/opensips/opensips_fifo")

#### URI module
loadmodule "uri.so"
modparam("uri", "use_uri_table", 0)

#### USeR LOCation module
loadmodule "usrloc.so"
modparam("usrloc", "working_mode_preset", "single-instance-no-db")
### Amar - Adding usrloc params
modparam("usrloc", "timer_interval", 1) # this is used to scan DB and remove AOR entries exactly at expiry
#modparam("usrloc", "cseq_delay", 0) # This is used to not allow refresh REGISTERs

#### REGISTRAR module
loadmodule "registrar.so"

loadmodule "auth.so"
loadmodule "textops.so"
loadmodule "avpops.so"

# Send event to subscribers
loadmodule "event_datagram.so"

####### Routing Logic ########

# main request routing logic

route {
  if (is_method("OPTIONS")) {
    if ($si == '172.16.5.72') {
      force_rport();
    }
    sl_send_reply("200", "OK");
    exit;
  }
  if (!is_method("REGISTER")) {
    xlog("L_NOTICE","C5::$avp(nat)::$proto:$si:$sp::$avp(account_code)::$rm::501 Not Implemented");
    sl_send_reply("501","Not Implemented");
  }

  # Not checking save() return code, Opensips already logs errors, main Opensips should not receive anything
  save("location", "r"); # r flag make Opensips does not reply with 200 Ok
  exit;
}

# vim: ts=2:sw=2
```
2. /etc/opensips/opensipsctlrc
```
# $Id$
#
# The OpenSIPS configuration file for the control tools.
#
# Here you can set variables used in the opensipsctl and opensipsdbctl setup
# scripts. Per default all variables here are commented out, the control tools
# will use their internal default values.

## your SIP domain
# SIP_DOMAIN=opensips.org

## chrooted directory
# $CHROOT_DIR="/path/to/chrooted/directory"

## database type: MYSQL, PGSQL, ORACLE, DB_BERKELEY, DBTEXT, or SQLITE
## by default none is loaded
# If you want to setup a database with opensipsdbctl, you must at least specify
# this parameter.
# DBENGINE=MYSQL

## database port (PostgreSQL=5432 default; MYSQL=3306 default)
# DBPORT=3306

## database host
# DBHOST=localhost

## database name (for ORACLE this is TNS name)
# DBNAME=opensips

# database path used by dbtext, db_berkeley, or sqlite
# DB_PATH="/usr/local/etc/opensips/dbtext"

## database read/write user
# DBRWUSER=opensips

## password for database read/write user
# DBRWPW="opensipsrw"

## engine type for the MySQL/MariaDB tabels (default InnoDB)
# MYSQL_ENGINE="MyISAM"

## database super user (for ORACLE this is 'scheme-creator' user)
# DBROOTUSER="root"

# user name column
# USERCOL="username"

## for testing / development
#ETCDIR=etc/

# SQL definitions
# If you change this definitions here, then you must change them 
# in db/schema/entities.xml too.
# FIXME

# FOREVER="2020-05-28 21:32:15"
# DEFAULT_ALIASES_EXPIRES=$FOREVER
# DEFAULT_Q="1.0"
# DEFAULT_CALLID="Default-Call-ID"
# DEFAULT_CSEQ="13"
# DEFAULT_LOCATION_EXPIRES=$FOREVER


# Program to calculate a message-digest fingerprint 
# MD5="md5sum"

# awk tool
# AWK="awk"

# gdb tool
# GDB="gdb"

# grep tool
# GREP="grep"

# sed tool
# SED="sed"


# Describe what additional tables to install. Valid values for the variables
# below are yes/no/ask. With ask (default) it will interactively ask the user
# for an answer, while yes/no allow for automated, unassisted installs.
#

# If to install tables for the modules in the EXTRA_MODULES variable.
# INSTALL_EXTRA_TABLES=ask

# If to install presence related tables.
# INSTALL_PRESENCE_TABLES=ask

# Define what module tables should be installed.
# If you use the postgres database and want to change the installed tables,
# then you must also adjust the STANDARD_TABLES or EXTRA_TABLES variable 
# accordingly in the opensipsdbctl.base script.

# opensips standard modules
# STANDARD_MODULES="standard acc domain group permissions usrloc 
#                   msilo alias_db uri_db speeddial avpops auth_db dialog 
#                   dispatcher dialplan drouting nathelper load_balancer"

# opensips extra modules
# EXTRA_MODULES="imc cpl siptrace domainpolicy carrierroute userblacklist 
#                 b2b registrant call_center fraud_detection cachedb_sql"


## type of aliases used: DB - database aliases
## - default: none
# ALIASES_TYPE="DB"

## control engine: FIFO or UNIXSOCK
## - default FIFO
CTLENGINE=FIFO

## path to FIFO file
OSIPS_FIFO="/var/run/opensips/opensips_fifo"

## MI_CONNECTOR control engine: FIFO, UNIXSOCK, UDP, XMLRPC
# MI_CONNECTOR=FIFO:/tmp/opensips_fifo
# MI_CONNECTOR=UNIXSOCK:/tmp/opensips.sock
# MI_CONNECTOR=UDP:192.168.2.133:8000
# MI_CONNECTOR=XMLRPC:192.168.2.133:8000

## check ACL names; default on (1); off (0)
# VERIFY_ACL=1

## ACL names - if VERIFY_ACL is set, only the ACL names from below list
## are accepted
# ACL_GROUPS="local ld int voicemail free-pstn"

## verbose - debug purposes - default '0'
# VERBOSE=1

## do (1) or don't (0) store plaintext passwords
## in the subscriber table - default '1'
# STORE_PLAINTEXT_PW=0

## do not display the output highlighted
# NOHLPRINT=1

## OPENSIPS START Options
## PID file path - default is: /var/run/opensips.pid
# PID_FILE=/var/run/opensips.pid

## Extra start options - default is: not set
# example: start opensips with 64MB share memory: STARTOPTIONS="-m 64"
# STARTOPTIONS=

```

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
