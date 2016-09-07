### vTemplates

#### Introduction

A vTemplate is a blueprint for a new vDisk in the Open vStorage
Cluster.

#### vTemplate Actions

##### <a name="createfromvtemplate"</a>
Create from vTemplate

Create from vTemplate allows to create one or more vDisks at once by
cloning the vDisk. You can give a name, the amount of vDisks to create at
once and the start sequence number. In case more than 1 vDisk is
created you can select multiple Storage Routers onto which the new vDisk will
be created. The vDisks will be created in a round-robin fashion on
all the selected Storage Routers.

##### Delete

Delete the vTemplate from the Open vStorage Cluster. Deleting a
vTemplate can only be done if no vDisks which are created from the
vTemplate exist in the Open vStorage Cluster. The Delete-action can not
be undone.