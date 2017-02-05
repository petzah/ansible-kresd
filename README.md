# ansible-kresd

Template combines d_kresd -> g_kresd -> h_kresd -> kresd hashes (see configure.j2 template)


in default/main.yml:
```yaml
---
d_kresd:
  kresd_tls:
    systemd:
      socket:
        Socket:
          ListenStream1: ''
          ListenStream2: '[::1]:853'
          ListenStream3: '127.0.0.1:853'
```   

Let's say you have in your group_vars directory file ovh with:
```yaml
---
g_kresd:
  kresd:
    systemd:
      socket:
        Socket:
          ListenDatagram1: '' # reset all.. systemd.socket(5)
          ListenDatagram2: '[::1]:5455'
          ListenDatagram3: '127.0.0.1:5455'
          ListenStream2: '127.0.0.1:5455'
```
and all hosts within group ovh will be listening on tcp ipv4+ipv6 localhost 853 for DNS-TLS
(inherited from default/main.yml) and udp ipv4+ipv6 localhost port 5455 and tcp ipv4 5455 (inherited from g_kresd)

For host specific (host_vars directory) use h_kresd

To override port 853 with 899 for specific host
```yaml
---
h_kresd:
  kresd_tls:
    systemd:
      socket:
        Socket:
          ListenStream2: '[::1]:899'
          ListenStream3: '127.0.0.1:899'
```

Use configuration options as in kresd documentation
(notice that dot '.' needs to be replaced with two underscores)
Example kresd.conf configuration:
```yaml
---
g_kresd:
  kresd:
    conf:
      privileged:
        options:
          net__ipv6: 'false'
      unprivileged:
        options:
          trust_anchors__hold_down_time: '30 * day'
        actions:
          verbose: 'false'
```
