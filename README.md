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
