# full-routaas
🐣Ultra minimal🐣 BGP IPv4 Unicast Full Route "as a Service" for Docker container.

## Ultra minimal images.

full route images's size is "102MB"
```bash
# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
nnao45/full-routaas   latest              f62fc829754b        22 minutes ago      102MB
<none>                <none>              db963eb06590        22 minutes ago      949MB
```

RIB file size's size is "85MB"...so, full-routaas container size is "17MB" !!
```bash
# ls -lh rib.20180418.0000
-rw-r--r-- 1 root root 85M  4月 18 09:00 rib.20180418.0000
```

## Demo

Nework Diagram.
```bash
+---------------------------------+                            +---------------------------------+
|  Bob                            |                            | Alice                           |
|                                 |                            |                                 |
|   IP Addres   192.168.0.1       |                            |    IP Addres   192.168.0.2      |
|   Subnet mask 255.255.255.0     +----------------------------+    Subnet mask 255.255.255.0    |
|   AS          65555             |                            |    AS          65000            |
|   Device      full-routaaas     |                            |    Device      IOS-XRv          |
|                                 |                            |                                 |
+---------------------------------+                            +---------------------------------+
```

full-routaas Config is config.tml.
if you run your env, change config.tml's paramater
```bash
[bgpdconfig]
  as = 65555
  router-id = "192.168.0.1"

[bgpdconfig.mrt-config]
  best-path = false
  skip-v4 = false
  skip-v6 = true
  next-hop = "nil"

[[bgpdconfig.neighbor-config]]
  peer-as = 65000
  neighbor-address = "192.168.0.2"
  peer-type = "external"
```

Bob Router(full-routaas mem4GB CPU1)  
```bash
Bob# docker run -it --rm --privileged -p 179:179 nnao45/full-routaas:latest
INFO[0000] Add a peer configuration for:192.168.0.2      Topic=Peer
INFO[0000] MRT injection file is                        
INFO[0000] Running full-routaas version 1.0.0 !!        
INFO[0018] Peer Up                                       Key=192.168.0.2 State=BGP_FSM_OPENCONFIRM Topic=Peer
INFO[0073] MRT injection complete!! 
```

Alice Router(IOS-XRv ver5.3.0-1 mem4GB CPU1)
```bash
Alice# sho bgp ipv4 unicast summary | begin Neighbor
Neighbor        Spk    AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down  St/PfxRcd
192.168.0.1       0 65555 2573601      58      112    0    0 00:01:26     696234
```

My env, Total route advertisement time is 1 minutes 26sec. 😉 so fast!!
