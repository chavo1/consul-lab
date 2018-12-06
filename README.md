# consul-vagrant

## Starting the agent in the first node
- Login in consul01
```
$ vagrant ssh consul01
$ sudo su -
$ cd /vagrant
```
- Execute consul agent command
```
consul agent -server -bootstrap-expect=1 \
    -data-dir=/tmp/consul -node=agent-one -bind=192.168.56.51 \
    -enable-script-checks=true -config-dir=consul.d
```
## Starting the agent in the second node
- Login in consul02
```
$ vagrant ssh consul02
$ sudo su -
$ cd /vagrant
```
- Execute consul agent command
```
consul agent -data-dir=/tmp/consul -node=agent-two \
    -bind=192.168.56.52 -enable-script-checks=true -config-dir=consul.d
```
### From the "consul01" join the cluster and check the members
```
$ consul join 192.168.56.52
Successfully joined cluster by contacting 1 nodes.
$ consul members
Node       Address             Status  Type    Build  Protocol  DC   Segment
agent-one  192.168.56.51:8301  alive   server  1.4.0  2         dc1  <all>
agent-two  192.168.56.52:8301  alive   client  1.4.0  2         dc1
```
- From the first node we can query for the address of the node "agent-two" 
```
dig @127.0.0.1 -p 8600 agent-two.node.consul

; <<>> DiG 9.10.3-P4-Ubuntu <<>> @127.0.0.1 -p 8600 agent-two.node.consul
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52857
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;agent-two.node.consul.         IN      A

;; ANSWER SECTION:
agent-two.node.consul.  0       IN      A       192.168.56.52

;; ADDITIONAL SECTION:
agent-two.node.consul.  0       IN      TXT     "consul-network-segment="

;; Query time: 0 msec
;; SERVER: 127.0.0.1#8600(127.0.0.1)
;; WHEN: Wed Dec 05 12:45:15 UTC 2018
;; MSG SIZE  rcvd: 102
```
- To leave a cluster use "Ctrl-C"
## Defining Checks - create two files into the consul.d directory
```
echo '{"check": {"name": "ping",
  "args": ["ping", "-c1", "google.com"], "interval": "30s"}}' \
  >consul.d/ping.json

echo '{"service": {"name": "web", "tags": ["rails"], "port": 80,
  "check": {"args": ["curl", "localhost"], "interval": "10s"}}}' \
  >consul.d/web.json
  ```
## Restart the second agent, reload it with "consul reload".
```
    [INFO] agent: Synced service 'web'
    [INFO] agent: Synced check 'service:web'
    [INFO] agent: Synced check 'ping'
    [WARN] Check 'service:web' is now critical
```
# Check the health status via API
```
curl http://localhost:8500/v1/health/state/critical
```


