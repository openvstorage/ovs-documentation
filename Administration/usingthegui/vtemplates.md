### vTemplates

#### Introduction

A vTemplate is a blueprint for a new vDisk in the Open vStorage
Cluster.

#### vTemplate Actions

##### <a name="createfromvtemplate"</a>
Create from vTemplate

Create from vTemplate allows to create one or more vDisks at once by
cloning the vDisk. The new vMachine will have the same
amount of RAM and VPU as the original vMachine. You can give a name, a
description for the new vMachine, the amount of vMachines to create at
once and the start sequence number. In case more than 1 vMachine is
created you can select multiple Hosts onto which the new vMachines will
be created. The vMachines will be created in a round-robin fashion on
all the selected Hosts.

##### Delete

Delete the vTemplate from the Open vStorage Cluster. Deleting a
vTemplate can only be done if no vMachines which are created from the
vTemplate exist in the Open vStorage Cluster. The Delete-action can not
be undone.