# Ceph Example Bundles from Ceph Day London 2019

## Overview

The bundles in this repository model two discrete Ceph deployments
in two regions - us-east and us-west.

Each deployment uses the other as backup for disaster recovery,
enabling use of RBD mirroring and Ceph RADOS Gateway replication
for replication of block and object storage between regions.

## Deployment instructions

Add the model and deploy the bundle for the us-west region:

```
juju add-model us-west
juju deploy us-west.yaml
```

Add the model and deploy the bundle for the us-east region:

```
juju add-model us-east
juju deploy us-east.yaml
```

Setup the CMR offers for the RADOS Gateway and RBD mirror
services in the us-east region:

```
juju switch us-east
juju offer rgw-us-east:master
juju offer rbd-mirror-us-east:ceph-remote

```

Setup the CMR offer for the RBD mirror service in the
us-west region:

```
juju switch us-west
juju offer rbd-mirror-us-west:ceph-remote
```

Consume and relate to services from us-east region in the
us-west region:

```
juju switch us-west
juju consume admin/us-east.rgw-us-east
juju consume admin/us-east.rbd-mirror-us-east
juju add-relation ceph-mon rbd-mirror-us-east
juju add-relation rgw-us-west:slave rgw-us-east:master
```

Consume and relate to services from the us-west region in
the us-east region:

```
juju switch us-east
juju consume admin/us-west.rbd-mirror-us-west
juju add-relation ceph-mon rbd-mirror-us-west
```

