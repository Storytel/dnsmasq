# dnsmasq

We use [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) as a lightweight DNS server for discovery of fixed services within our infrastructure. e.g. [Eureka](https://github.com/Netflix/eureka)

Provided here is a minimal [docker](https://www.docker.com/) image designed to configure itself from a mapped directory of `.conf` files.

## How to use

Starting a container from this image is simple. We run it directly on the host's network stack so the host can act as a DNS for other services in the network.

```
docker run --name dnsmasq --cap-add=NET_ADMIN --net=host -v /etc/dnsmasq:/etc/dnsmasq storytel/dnsmasq
```

We can then add multiple `.conf` files to the `/etc/dnsmasq` folder to provide layered configuration. This layering is useful to allow multiple services to add their own configuration files.

```
# 0.base.conf

domain-needed
bogus-priv
no-hosts
keep-in-foreground
no-resolv
expand-hosts
server=8.8.8.8
server=8.8.4.4
```

```
# 1.eureka.conf
address=/001.eureka.storytel/10.10.10.21
address=/002.eureka.storytel/10.10.10.22
address=/003.eureka.storytel/10.10.10.23
txt-record=txt.global.eureka.storytel,sweden.eureka.storytel
txt-record=txt.sweden.eureka.storytel,10.10.10.21,10.10.10.22,10.10.10.23
```

```
# 2.etcd.conf
address=/001.etcd.storytel/10.10.10.21
address=/002.etcd.storytel/10.10.10.22
address=/003.etcd.storytel/10.10.10.23
```
