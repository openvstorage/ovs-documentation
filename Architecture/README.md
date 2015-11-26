## Architecture

### Introduction
The Open vStorage architecture consists out of 3 big components:
* [The Framework](Framework/README.md): the management interface (GUI, API) of the VolumeDriver. It provides the necessary logic to setup and maintain an Open vStorage Cluster.
* [The VolumeDriver](VolumeDriver/README.md): this component exposes the [different interfaces](Volumedriver/Interfaces/README.md) such as the Virtual Machine Storage Backend, the Virtual NAS, the block interface and the distributed DB interface) and implements fast, [Distributed Block Layer](VolumeDriver/Blocklayer/README.md).
* [ALBA](ALBA/README.md): this components is responsible for the distributed self healing storage layer. It connects to (local) disks and object storage solutions.
