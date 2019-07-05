## Introduction

As Jeffrey and I are leaving, these documentations will serve as a guide for aspiring developers to get a jumpstart in the framework as we left it.
It contains some basic information to get you faster up to speed, some clarifications on why we (didn't) implement things a certain way, guidance on our branches system and other funfacts.

### Structure of the framework and its repos

#### Framework
This is the core of the ovs  [framework](todo insert link). Contains all logic for
 - CLI and the GUI work
 - plugin loading
 - service management
 - configuration management
 - DAL stuff
 

 
 #### Framework-extensions
Framework extensions contain logic that  
 - is shared across multiple repos such as the framework, asd-manager or iscsi-manager,
 and should be installed regardless of which one of these is installed.
 - function as an interface and should be implemented by one of these managers
 
 #### Alba-asd and iSCSI manager
 These managers contain logic to function as a standalone nodes
 
 #### alba-plugin and iscsi-plugin
 The plugins will allow the managers to work alongside the regular framework and integrate them in the GUI
 
 #### integrationtests
 Once used as tests after nightly installs, these tests have been abandoned due to being outdated
 
 #### openvstorage-healthcheck
 Used as a tool to measure the 'health' of an ovs cluster
 
 
 