[![GitHub release](https://img.shields.io/github/release/cybozu-go/coil.svg?maxAge=60)][releases]
[![CircleCI](https://circleci.com/gh/cybozu-go/coil.svg?style=svg)](https://circleci.com/gh/cybozu-go/coil)
[![PkgGoDev](https://pkg.go.dev/badge/github.com/cybozu-go/coil?tab=overview)](https://pkg.go.dev/github.com/cybozu-go/coil?tab=overview)
[![Go Report Card](https://goreportcard.com/badge/github.com/cybozu-go/coil)](https://goreportcard.com/report/github.com/cybozu-go/coil)

Coil
====

**Coil** is a [CNI][]-based network plugin for Kubernetes.

Coil is designed with respect to the UNIX philosophy.  You can combine
Coil with any routing software and/or any network policy implementation.

Coil allows to define multiple IP address pools.  You can define a pool of
global IPv4 addresses for a small number of pods and another pool of
private IPv4 addresses for the remaining pods.

Status
------

Version 2 is under **active development**.  It conforms to [CNI spec 0.4.0](https://github.com/containernetworking/cni/blob/spec-v0.4.0/SPEC.md).

Version 1 is maintained in [release-1.1](https://github.com/cybozu-go/coil/tree/release-1.1) branch.

Dependencies
------------

- Kubernetes Version: 1.18
    - Other versions are likely to work, but not tested.

- (Optional) Routing software
    - Coil has a simple routing software for flat L2 networks.
    - If your network is not flat, use BIRD or similar software to advertise the routes.

Features
--------

- Address pools

    Coil can have multiple pools of IP addresses for different purposes.
    By setting a special annotation to a namespace, you can specify a pool
    for the pods in that namespace.

- IPv4/IPv6 dual stack

    In addition to IPv4-only and IPv6-only stacks, Coil can define dual stack
    address pools.

- Running with any routing software

    Coil provides a simple router for clusters where all the nodes are in
    a flat L2 network.  This router, called `coil-router`, is optional.

    For more complex networks, Coil exports routing information to an
    unused kernel routing table.  By importing the routes from the table,
    any routing software can advertise them.

- On-demand NAT for egress traffics

    Coil can implement SNAT _on_ Kubernetes.  You can define SNAT routers
    for external networks as many as you want.

    Only selected pods can communicate with external networks via SNAT
    routers.

Refer to [the design document](./docs/design.md) for more information on these features.

Usage examples
--------------

[Project Neco](https://blog.kintone.io/entry/neco) uses Coil with these software:

- [BIRD][] to advertise routes over BGP,
- [MetalLB][] to implement [LoadBalancer] Service, and
- [Calico][] to implement [NetworkPolicy][].

Coil should also be able to work with [Cilium][] through its [generic veth chaining](https://docs.cilium.io/en/v1.8/gettingstarted/cni-chaining-generic-veth/) feature.

Programs
--------

This repository contains these programs:

- `coil`: [CNI][] plugin.  It simply delegates requests to `coild`.
- `coild`: A gRPC server to accept requests from `coil`.
- `coil-router`: An optional simple router for a flat L2 network.
- `coil-installer`: installs `coil` and CNI configuration file.
- `coil-controller`: watches kubernetes resources for coil.
- `coil-egress`: controls SNAT router pods.

Install
-------

The official Docker image is on [Quay.io](https://quay.io/repository/cybozu/coil)

TBD

Documentation
-------------

The user manual is [./docs/usage.md](./docs/usage.md).

[docs](docs/) directory contains other documents about designs and specifications.

[mtest/bird.conf](mtest/bird.conf) is an example configuration for [BIRD][] to make it work with coil.

License
-------

MIT

[releases]: https://github.com/cybozu-go/coil/releases
[CNI]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/
[BIRD]: https://bird.network.cz/
[LoadBalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer
[NetworkPolicy]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[MetalLB]: https://metallb.universe.tf
[Calico]: https://www.projectcalico.org
[Cilium]: https://cilium.io/
