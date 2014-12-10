## Swarm: a Docker-native clustering system [![Build Status](https://travis-ci.org/docker/swarm.svg?branch=master)](https://travis-ci.org/docker/swarm)

![Docker Swarm Logo](logo.png?raw=true "Docker Swarm Logo")

`swarm` is a simple tool which controls a cluster of Docker hosts and exposes it as a single "virtual" host.

`swarm` uses the standard Docker API as its frontend, which means any tool which speaks Docker can control swarm transparently: dokku, fig, krane, flynn, deis, docker-ui, shipyard, drone.io, Jenkins... and of course the Docker client itself.

Like the other Docker projects, `swarm` follows the "batteries included but removable" principle. It ships with a simple scheduling backend out of the box. The goal is to provide a smooth out-of-box experience for simple use cases, and allow swapping in more powerful backends, like `Mesos`, for large scale production deployments.

### Installation

######1 - Download and install the current source code.
```sh
go get -u github.com/docker/swarm
```

######2 - Nodes setup
The only requirement for Swarm nodes is to run a regular Docker daemon.

In order for Swarm to be able to communicate with its nodes, they must bind on a network interface.
This can be achieved by starting Docker with the `-H` flag (e.g. `-H 0.0.0.0:2375`).

Currently, nodes must be running the Docker **master** version.
Master binaries are available here: https://master.dockerproject.com/

### Example usage: using the hosted discovery service

```bash
# create a cluster
$ swarm create
6856663cdefdec325839a4b7e1de38e8

# on each of your nodes, start the swarm agent
#  <node_ip> doesn't have to be public (eg. 192.168.0.X),
#  as long as the other nodes can reach it, it is fine.
$ swarm join --discovery token://6856663cdefdec325839a4b7e1de38e8 --addr=<node_ip:2375>

# start the manager on any machine or your laptop
$ swarm manage --discovery token://6856663cdefdec325839a4b7e1de38e8 --addr=<swarm_ip:swarm_port>

# use the regular docker cli
$ docker -H <swarm_ip:swarm_port> info
$ docker -H <swarm_ip:swarm_port> run ... 
$ docker -H <swarm_ip:swarm_port> ps 
$ docker -H <swarm_ip:swarm_port> logs ...
...

# list nodes in your cluster
$ swarm list --discovery token://6856663cdefdec325839a4b7e1de38e8
http://<node_ip:2375>
```

### Example usage: using a static file describing the cluster

```bash
# for each of your nodes, add a line to a file
#  <node_ip> doesn't have to be public (eg. 192.168.0.X),
#  as long as the other nodes can reach it, it is fine.
$ echo <node_ip:2375> >> /tmp/my_cluster

# start the manager on any machine or your laptop
$ swarm manage --discovery file:///tmp/my_cluster --addr=<swarm_ip:swarm_port>

# use the regular docker cli
$ docker -H <swarm_ip:swarm_port> info
$ docker -H <swarm_ip:swarm_port> run ... 
$ docker -H <swarm_ip:swarm_port> ps 
$ docker -H <swarm_ip:swarm_port> logs ...
...

# list nodes in your cluster
$ swarm list --discovery file:///tmp/my_cluster
http://<node_ip:2375>
```

### Example usage: using etcd

```bash
# on each of your nodes, start the swarm agent
#  <node_ip> doesn't have to be public (eg. 192.168.0.X),
#  as long as the other nodes can reach it, it is fine.
$ swarm join --discovery etcd://<etcd_ip>/>path> --addr=<node_ip:2375>

# start the manager on any machine or your laptop
$ swarm manage --discovery etcd://<etcd_ip>/>path> --addr=<swarm_ip:swarm_port>

# use the regular docker cli
$ docker -H <swarm_ip:swarm_port> info
$ docker -H <swarm_ip:swarm_port> run ... 
$ docker -H <swarm_ip:swarm_port> ps 
$ docker -H <swarm_ip:swarm_port> logs ...
...

# list nodes in your cluster
$ swarm list --discovery etcd://<etcd_ip>/>path>
http://<node_ip:2375>
```

### TLS

Swarm supports TLS authentication between the CLI and Swarm but also between Swarm and the Docker nodes.

In order to enable TLS, the same command line options as Docker can be specified:

`swarm manage --tlsverify --tlscacert=<CACERT> --tlscert=<CERT> --tlskey=<KEY> [...]`

Please refer to the [Docker documentation](https://docs.docker.com/articles/https/) for more information on how
to set up TLS authentication on Docker and generating the certificates.

Note that Swarm certificates must be generated with`extendedKeyUsage = clientAuth,serverAuth`.

## Participating

We welcome pull requests and patches; come say hi on IRC, #docker-swarm on freenode.

## Creators

**Andrea Luzzardi**

- <http://twitter.com/aluzzardi>
- <http://github.com/aluzzardi>

**Victor Vieux**

- <http://twitter.com/vieux>
- <http://github.com/vieux>

## Copyright and license

Code and documentation copyright 2014 Docker, inc. Code released under the Apache 2.0 license.
Docs released under Creative commons.

