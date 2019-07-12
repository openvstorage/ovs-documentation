## Introduction

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
 These managers contain logic to function as a standalone microservice. Can run on different nodes, regardles of where the framework node is running. 
 When running such a service on the same node as the framework, installation of a manager plugin enables accessing the managers in the GUI via HTTP API
 
 #### alba-plugin and iscsi-plugin
 The plugins will allow the managers to work alongside the regular framework and integrate them in the GUI
 
 #### integrationtests
 Once used as tests after nightly installs, these tests have been abandoned due to being outdated
 
 #### openvstorage-healthcheck
 Used as a tool to measure the 'health' of an ovs cluster
 
 
 