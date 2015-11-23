#### The distributed block layer
The distributed block layer is the core of the Open vStorage solution. It sits between the Virtual Filesystem Layer offered by the [interfaces](interfaces.md) and the [distributed storage layer](../ALBA/README.md). It provide the [read cache](#readcache) functionality, the write buffers(#writebuffer), the Distributed Transaction Log(#DTL) and the shared meta data.

Open vStorage is the first storage system in the world to combine both a Location Based Storage and Log Structured Storage approach.


{% include "writebuffer.md" %}

{% include "DTL.md" %}

{% include "readcache.md" %}

