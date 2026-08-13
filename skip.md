# Introduction

# References

1. Asterisk
    * AMI
        * [AMI](https://vicistack.com/blog/asterisk-manager-interface-guide/)
        * [AMI events](https://docs.asterisk.org/Latest_API/API_Documentation/AMI_Events/)
    * ARI
        * [ARI]()
        * [ARI events]()
2. Freeswitch
3. Opensips
4. Kamailo 

# Watcher

## Technologies

* Watcher is built in Ruby Gems (Back-End).
* Watcher uses Thread safe implementations to manage conenction.
* Watcher use TCP/UDP sockets to establish connections with the respective APIs.
* Watcher runs as a debian service.
* Watcher is build into a debian package.

## Features:



## Architecture 

![skip](/home/gollum/wiki/images/skip_1.png)

# Why it beats the obvious alternatives:

## Logic


# Configuration

## Asterisk

Asterisk needs to support events through the manager module.
1. manager.conf
```text
; /etc/asterisk/manager.conf
[general]
enabled = yes
port = 5038
; ACCEPTABLE: Specific internal network interface
; bindaddr = 10.0.0.5
; DANGEROUS: Listen on all interfaces
; bindaddr = 0.0.0.0
; GOOD: Only local connections
bindaddr = 127.0.0.1    ; IMPORTANT: Only listen on localhost by default
displayconnects = yes
timestampevents = yes

[watcher]
secret = watcher
deny = 0.0.0.0/0.0.0.0 ; Deny everything first
permit = 127.0.0.1/255.255.255.255 ; Allow localhost
permit = 10.0.1.50/255.255.255.255 ; Allow the monitoring server
read = system,call,log,verbose,agent,user,config,dtmf,reporting,cdr,dialplan
write = system,call,agent,user,config,command,reporting,originate
writetimeout = 5000
```
2. sip.conf (for testing)
```text
[amar]
type=friend
secret=eiGh1oox
host=dynamic
nat=yes
context=internal
qualify=yes
dtmfmode=rfc2833
callerid=33970757076
```
3. Restart Asterisk
```text
systemctl restart asterisk
```

## Watcher
1. Installation
2. Post-Installation
```text
configuration:
  check_active_timeout: 15

core:
  address_pattern: ''
  check_active_command: ''

# Asterisk AMI
asterisk:
  ami:
    subscription:
      program_path: '/usr/bin/asterisk'
      user:         'watcher'
      protocol:     'tcp'
      ip:           '127.0.0.1'
      port:         5038
      events:
        # CALLS
        - 'NewChannel'
        - 'NewState'
        - 'Hangup'
        # HEALTH
        - 'FullyBooted'
        - 'Reload'
        - 'Shutdown'
        # REGISTRY
        - 'PeerStatus'
        - 'ContactStatus'
        - 'Registry'
  ari:
    subscription:
      program_path: '/usr/bin/asterisk'
      duration:     30
      protocol:     'udp'
      ip:           '127.0.0.1'
      port:         12345
      events:
        # CALLS
        - 'ChannelCreated'
        - 'StasisStart'
        - 'StasisEnd'
        # REGISTRY
        - 'EndpointStateChange'
        - 'ContactStatus'
        - 'PeerStatus'

# OpenSips Management
opensips:
  management:
    subscription:
      program_path: '/usr/sbin/opensipsctl'
      duration:     30
      protocol:     'udp'
      ip:           '127.0.0.1'
      port:         12345
      events:
        - 'ul'
        - 'drouting'

log:
  log_sql: false
  log4r:
    loggers:
      - name:  watcher
        level: DEBUG
        outputters:
          - stdout

    outputters:
      - type: StdoutOutputter
        name: stdout
        formatter:
          pattern     : '%l: %m'
          type        : PatternFormatter

events:
  nat_flag: '6'

control:
  port: 8080
```