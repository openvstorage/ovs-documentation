## The Open vStorage Internals

In this section we discuss the


* The [Volume Driver](VolumeDriver/README.md) exposes various [interfaces](Volumedriver/Interfaces/README.md), implements the [Distributed Block Layer](VolumeDriver/BlockLayer/README.md) and communicates to the Distributed Storage Layer, which can be Alba or other object storage systems
* [ALBA](ALBA/README.md), implements a self healing Distributed Storage Layer.
