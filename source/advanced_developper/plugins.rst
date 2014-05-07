.. _javadoc: http://snooze.inria.fr/community/javadoc/

.. _snooze-plugins: https://github.com/msimonin/snooze-plugins 

.. _plugins:

Plugins System Overview
-----------------------

Snooze allows you to use plugins to modify the default behaviour of the application.
This page will give you an overview of which components you can plug in Snooze.


General considerations
^^^^^^^^^^^^^^^^^^^^^^

This section will guide you through the general mechanisms used in Snooze to load its plugins.

Plugins in Snooze are jar files containing classes (one or a set of) compiled outside of the core system and injected at runtime into the system.
The plugins have to be located in *globals.pluginsDirectory* (see :ref:`configfile`).
This injection takes place after getting the parameters needed by the plugin from the configuration file (by default *snooze_node.cfg*).

A plugin contains a *main* class which derives from an abstract superclass (except for the monitoring estimator, see below).
This class must contain a constructor without parameter and a method *initialize* which is called right after the constructor and is responsible 
for settings the specific parameter of the plugin.

Below we give a short description of the part which can be used as plugin in Snooze.
You can refer to the javadoc (javadoc_) to figure out what is available inside your plugin learn from the snooze-plugins project (snooze-plugins_).

Virtual Machines and Hosts Monitoring Estimator
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Virtual machine monitoring estimator has to implement *VirtualMachineMonitoringEstimator*.

This class is responsible for the estimation made on virtual machines resources (CPU, Memory and network).

* HostMonitor monitoring estimator has to implement *HostMonitoringEstimator*.

This class is responsible for the estimation made on host resources (defined in the configuration file).


Resource Demand Estimator
^^^^^^^^^^^^^^^^^^^^^^^^^^

Resource demand estimator is used for estimate the node resources utilization (Groupmanagers, Localcontroller, Virtualmachines), 
it is intensively used in placement algorithm (e.g sort the local controller, figure out if a group manager has enough capacity ...).

* A new resource demand estimator has to extend *ResourceDemandEstimator*.


GroupManager Placement Policy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GroupManager Placement Policy is responsible for placing virtual machines on local controllers.

* A new group manager placement policy has to extend *PlacementPolicy*.


GroupLeader Dispatching Policy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GroupLeader Dispatching Policy is responsible for dispatching virtual machines on group managers.

* A new group leader dispatching policy has to extend *DispatchingPolicy*.

GroupLeader Assignement Policy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

GroupLeader Assignement Policy is responsible for assignement of local controllers on group managers.

* A new group leader assignement policy has to extend *AssignementPolicy*.


Anomaly Detector 
^^^^^^^^^^^^^^^^^

Anomaly detector is responsible for detecting anomaly on the local controller it runs. 
In case of detection it sends a message to the group manager.

* A new anomaly detector has to extend  *AnomalyDetector*. 

Anomaly Resolver
^^^^^^^^^^^^^^^^^

Anomaly resolver is responsible for resolving an anomaly. Anomaly resolver receives the anomaly message sent by the anomaly local controller.

* A new anomaly resolver has to extend *AnomalyResolver*.


Local Controller Host Monitor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A host monitor is responsible for getting metrics for the local controller it runs on. You can plug as many monitor you want (be reasonable though).

* A new Host Monitor have to extend *HostMonitor*.
