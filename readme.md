### Setup Notes:

#### KEA

- default ubuntu repo does not have status-get command, need to add cloudsmith repo and install newer versions (2.1+)
- - installed isc-kea-admin, common, ctrl-agent, dhcp-ddns-server, dhcp4-server, doc
- make sure kea-ctrl-agent config sockets match config for dhcp, ddns
- kea-ddns: add tsig key from ddns-confgen, zone names end in dot notation for bind
- kea-dhcp4: need hooks libraries for stork, need qualifying suffix for ddns

#### BIND

- set control for rndc and statistics for stork in named.conf
```
controls {
  inet 127.0.0.1 allow {localhost; };
  };
statistics-channels {
        inet 127.0.0.1 port 8053 allow { 127.0.0.1; };
};
```
- use ddns-confgen to create TSIG key, this goes in named.conf.local config, kea-ddns config, as well as create a file with content for nsupdate testing
- options file contains forwarders, as well as setup logging for troubleshooting, logrotate config needed to rotate logs
- options file also filter/stop listening for ipv6 to stop log noise.  Also need to add '-4' flag to execstart in service
- recommended putting zones files in writtable area /var/lib/bind/,  mkdir (ex primary), chown and chgrp to bind to be sure can write, this is also critical for DDNS to create jnl files for zones
- also for ubuntu may need to disable apparmor for named for writing, not 100% this was needed after mv from /etc/bind to /var/lib


#### STORK

- install cloudsmith repo, install agent and server for standalone
- agent: change port in agent.env to use different than server (ex 8081), skil tls cert verify
- server: install postgresql, change auth for default user in psql from IDENT to password, follow docs to setup using stork-tool, add new db pass to server.env