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
### AMI
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

### ARI
1. http.conf
```text
[general]
enabled = yes
bindaddr = 0.0.0.0
bindport = 8088
```
2. ari.conf (ARI users)
```text
[general]
enabled = yes
pretty = yes

[watcher]
type = user
read_only = no
password = watcher
```
3. Add missing modules modules.conf
```text
load = res_stasis.so
load = res_ari.so
load = res_http_websocket.so
load = res_ari_events.so
```
4. Restart Asterisk
```text
systemctl restart asterisk
```

## Watcher

1. Installation

2. Post-Installation (/etc/watcher/watcher.yaml)
```text
configuration:
  check_active_timeout: 15

core:
  address_pattern: ''
  check_active_command: '/bin/true'

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
        # REGISTRY
        - 'PeerStatus'
        - 'ContactStatus'
        - 'Registry'
        # CALLS
        - 'NewChannel'
        - 'NewState'
        - 'Hangup'
        # HEALTH
        - 'FullyBooted'
        - 'Reload'
        - 'Shutdown'
  ari:
    subscription:
      program_path: '/usr/bin/asterisk'
      user:         'watcher'
      protocol:     'websocket'
      ip:           '127.0.0.1'
      port:         8088
      events:
        # REGISTRY
        - 'EndpointStateChange'
        - 'ContactStatus'
        - 'PeerStatus'
        # CALLS
        - 'ChannelCreated'
        - 'StasisStart'
        - 'StasisEnd'

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

### Launch
1. Daemon

2. Manually
```text
cd watcher/
ruby -Ilib bin/watcher
```

# DEV
## Threads
1. The main thread
```text
# Main Processing Thread
    def start()
      init unless @initialized
      WATCHER::Logger.debug('Starting Watcher Processing Thread...')

      # Orchestration
      @threads << @ami.start
      #@threads << @opensips.start
      @threads << @graphite.start
      @threads << @kpis.start

      #@threads.each(&:join)

      while @running
        begin
          break unless @running

          process_asterisk_messages
          process_opensips_messages

        rescue StandardError => e
          WATCHER::Logger.fatal("Watcher Main Processing THread returned error #{e.message}")
          @running = false
        end
      end
    end
```
2. AMI Thread
```text
    # AMI connection Thread
    def start()
      return unless @host && @port

      @semaphore = Mutex.new # If placed inside the Thread, it creates a race condition, Mutex is synchronous

      @thread = Thread.new do
        WATCHER::Logger.debug("Starting AMI Connection Thread...")
        # Asynchronous
        # AMI uses TCP
        while @running
          begin
            raise "Watcher cannot connect to Asterisk AMI events with #{@asterisk_path}" if can_connect?
            connect unless @socket

            Timeout.timeout(NO_PACKET_TIMEOUT) do
              @semaphore.synchronize do
                read
              end
            end
            break unless @running

          rescue TimeoutError
            # Do Nothing
          rescue StandardError => e
            WATCHER::Logger.warn("AMI Connection Thread threw an error: #{e.message}")
          end
        end
      rescue StandardError => e
        WATCHER::Logger.error("Stoping AMI Connection Thread with error: #{e.message}")
      end
      # WATCHER::Logger.info('Starting AMI Connection Thread...')
    end
```

## Testing
1. REGISTER
```text
sipexer -register -vl 3 -co -com -ex 100 -fuser amar -fdomain sip.openvno.net -cb -ap "eiGh1oox" udp:10.44.81.209:5060
```