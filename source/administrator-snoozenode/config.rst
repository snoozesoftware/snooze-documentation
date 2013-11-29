Snoozenode Config File
----------------------

Snooze system configuration is done using its main configuration file /usr/share/snoozenode/configs/snooze_node.cfg. You should be able to find it on each node after installation. It is partitioned into several categories. you can find the description of each categorie below.

Node
^^^^

* *node.role*

Each node can play the role of either a bootstrap, groupmanager, or localcontroller. It is up to the system administrator to decide on the role and the number of nodes dedicated to a role during the system setup.

* *node.networkCapacity.Rx* and *node.networkCapacity.Tx*

 Available networking capacity (Rx and Tx) must be configured for each local controller (LC). It is set to 1 Gb (= 131072 kB) by default.


Networking
^^^^^^^^^^

* *network.listen.address*

Binds the Snooze service to the specified address. By default it listen on all interfaces.

* *network.listen.controlDataPort* 

Control data port is required for the REST communication on each node.

* *network.listen.monitoringDataPort*

Monitoring data port is used by the GMs and the GL to receive monitoring data from LCs (resp. GMs).

* *network.multicast.address*

Multicast address is used to periodically propagate heartbeat information.

* *network.multicast.groupManagerHeartbeatPort*

The multicast port on which a GM announces its presence (must be distinct on each GM).

* *network.virtualMachineSubnet*

Coma separated list of virtual machine subnets from which Snooze will assign IPs to VMs.

Httpd
^^^^^

* *httpd.maxNumberOfThreads* 

Adjust the maximum number of threads in the pool to handle incoming RESET requests.

* *httpd.maxNumberOfConnections* 

The maximum number of allowed connections.

* *httpd.minThreads*

Minimum number of active threads.

* *httpd.lowThreads*

Low number of active threads.

* *httpd.maxThreads* 

Maximum number of active threads.

* *httpd.maxQueued*

Max Idle Time (0 for infinite)

Hypervisor
^^^^^^^^^^
* *hypervisor.driver*

The name of the underlying hypervisor driver. It is set to qemu by default.

* *hypervisor.transport* 

The transfer method between the hypervisors (i.e. libvirt daemons). It is set to tcp by default. Note that the system has only been tested using the tcp transport for now! You will need to configure libvirtd accordingly to use other transport methods.

* *hypervisor.port* 

The local libvirtd listen port. LCs will use it to connect to contact libvirt.

* *hypervisor.migration.method*

You can choose different migration methods as supported by libvirt (see virDomainMigrateFlags for more details). Currently, Snooze supports live migration, migration with non-shared storage with full disk copy, and migration with non-shared storage with incremental copy. The latter is the default setting.

* *hypervisor.migration.timeout* 

In some hypervisors (e.g. KVM) migration can last forever if the number of pages that got dirty is larger than the number of pages that got transferred to the destination LC during the last transfer period. Snooze implements a watchdog mechanisms to force convergence in such situations. Therefore, a timeout must be specified above which the system will suspend the VMs and thus let them finish migrating (i.e. no dirty pages anymore).

Fault Tolerance
^^^^^^^^^^^^^^^

* *faultTolerance.zookeeper.hosts* 

You must give a list with at least one ZooKeeper host which will be used to coordinate the group leader (GL) election. 

* *faultTolerance.zookeeper.sessionTimeout*

The amount of time above with the ZooKeeper session is considered terminated.

* *faultTolerance.heartbeat.interval*

Heartbeat messages are periodically sent from the GL to the GMs and from GMs to the LCs. You can tweak the sending intervals using this parameter.

* *faultTolerance.heartbeat.timeout*

The amount of time above which the heartbeat is considered lost.

Database
^^^^^^^^


System management meta-data as well as monitoring information is stored at the GL as well as at the GMs. The database settings allow you to tweak some of the storage parameters.

* *database.type* 

System information can be either stored in-memory or on some persistent storage (e.g. MySQl, MongoDB, etc.). Currently only in-memory storage is supported.

* *database.numberOfEntriesPerGroupManager*

 In order to avoid running out of storage capacity you need to specify the maximum number of entries per group manager. For example, if set to 20 the GL will store 20 monitoring entries per GM and start overwriting the oldest ones in case the limit has been reached. In other words, the storage is implemented as a circular buffer. 

 * *database.numberOfEntriesPerVirtualMachine*

 Similarly to the previous setting the GM must be instructed to respect a certain upper bound on the number of monitoring entries per VM.


Monitoring
^^^^^^^^^^

* *monitoring.interval*

Controls the time interval at which monitoring data is sent from the LCs to GMs and GMs to GL. 

* *monitoring.timeout* 

The amount of time above which monitoring data is considered as lost. Note that the monitoring timeout is also used to detect GM and LC failures. For example, if GM monitoring data is lost the GL considers it as failed. Similarly, when LC monitoring data is lost it is considered as failed by the GM in charge.

* *monitoring.numberOfMonitoringEntries*

Overload and underload anomaly detection is performed based on aggregates. Particularly, each LC first collects a certain amount of monitoring data entries per VM period starting the anomaly detection. You can control this amount using this parameter.

* *monitoring.thresholds.*

For each resource (i.e. CPU, memory, and network) Snooze defines three thresholds (MIN, MID, and MAX). When the aggregated utilization in one of the resources falls below the MIN threshold the LC is considered underloaded. Similarly, if the utilization crosses the MAX threshold the LC is considered overloaded. The MID threshold is used to cap the max allowed used resource capacity. This allows to keep a buffer of spare resources to compensate during periods of high resource contention. For example if set to 0.5 at max 50% of the available resource capacity will be available to host VMs.

External Notifier
^^^^^^^^^^^^^^^^^

Snooze can send notification to the external world about what happens in the system.
See the rabbitmq section of the documentation to know how to configure the rabbitmq service.

* *external.notifier.transport*

Transport type to use for sending notification. Only RABBITMQ is supported.

* *external.notifier.address* 
* *external.notifier.port* 

Where to send notification. For example the address and the port of the rabbitmq broker.

* *external.notifier.username*
* *external.notifier.password* 

Credentials.

* *external.notifier.vhost*

Rabbitmq virtual host.


Estimation
^^^^^^^^^^^

* *estimator.static* 

True if estimations should be based on static values only. Particularly, if your VM requests 3 GB of RAM during submission but uses on average 2 GB only according to the collected monitoring data, the estimator would still consider the requested capacity when requested to do estimations.

* *estimator.sortNorm* 

Sorting VMs requires their resource usage vectors to be mapped to scalar values. Therefore different vector norms (e.g. L1, Euclid, Max) can be used.

* *estimator.numberOfMonitoringEntries* 

 The maximum number of monitoring entries per VM to consider in estimations. For example, it is possible to instruct the system to use the most recent 15 monitoring entries per VM in its estimations.

* *estimator.policy.* 

You can implement different estimators for each resource and choose between them using this parameter. Currently, estimations are based on averages of estimator.numberOfMonitoringEntries most recent values.

Group Leader Scheduler
^^^^^^^^^^^^^^^^^^^^^^

* *groupLeaderScheduler.assignmentPolicy* 

 When a LC attempts to join the hierarchy it needs to know which GM to join. The assignment policy is in charge of selecting the GM. Currently two assignment policies are implemented: RoundRobin and FirstFit. You can integrate you own assignment policies by implementing the provided assignment interface.

* *groupLeaderScheduler.dispatchingPolicy*

When a client attempts to submit a VC, its VMs need to be dispatched to GMs. The dispatching policy makes the GM choice according to the aggregated GM monitoring data. Currently two dispatching policies are implemented: RoundRobinSingleGroupManager and FirstFitSingleGroupManager. 

*Note that* aggregated information might be not sufficient to take exact dispatching decisions. For instance, when a client submits a VM requesting 2GB of memory and a GM reports 4GB available it does not necessary mean that the VM can be finally placed on this GM as its available memory could be distributed among multiple LCs (e.g. 4 LCs with each 1GB of RAM). Consequently, a list of candidate GMs can be returned by the dispatching policies. Based on this list, the GL performs a linear search by issuing VM placement requests to the GMs.

Existing dispatching policies return a list with a single element (i.e. GM). Consequently when the submission fails on the selected GM, no other will be tried. However, similarly to the assignment policies different dispatching policies can be integrated by implementing the appropriate dispatching interface.


Group manager scheduler
^^^^^^^^^^^^^^^^^^^^^^^

* *groupManagerScheduler.placementPolicy*

The placement policy is used to do initial assignment’s of VMs to LCs upon submission. Currently, two placement policies exist RoundRobin and FirstFit

* *groupManagerScheduler.relocation.* 

The overload and underload policies are triggered to resolve overload (resp. underload) anomaly situation. Both policies return a migration plan which specifies which VMs and to which LCs they need to be migrated to resolve the anomaly situations.

* *groupManagerScheduler.reconfiguration.enabled*

Complementary to the relocation mechanisms, reconfiguration can be enabled to periodically optimize the VM placement of moderately loaded VMs.

* *groupManagerScheduler.reconfiguration.policy*

You can implement any reconfiguration policy. However, currently Snooze implements a modified version of the Sercon consolidation algorithm. Please refer to Publications for more details.

* *groupManagerScheduler.reconfiguration.interval*

A cron expression which allows to provide a very flexible configuration of the reconfiguration interal (e.g. every night at 1 AM).

Submission
^^^^^^^^^^^

* *submission.dispatching.* 

When the client application attempts to submit a VC to the the GL, GL instructs the selected GMs to start VMs. If a GM is busy (e.g. it is in relocation or reconfiguration state), the submission requests are rejected by the GMs state machine. The GL implements retry logic to resent submission requests a predefined number of times within a predefined interval until it considers the VM submission request as failed. You can tune the retry behavior using the numberOfRetries and retryInterval parameters.

* *submission.collection.* 

Collection parameters control the VC submission response gathering. Particularly, as soon VMs have been accepted by a GM for submission, the GL will poll the GMs involved in the submission to retrieve the VM submission responses. Note that accepted for submission solely means that it has been added to the to be scheduled queue on the GM. The actual VM placement can take some time depending on the resource availability. For example, if LCs in a deep power saving state (e.g. shutdown) need to be woken up, typically several minutes are required until they become available and can be considered in the VM placement. It is absolutely crucial to set the numberOfRetries and retryInterval parameters carefully and we strongly advise you to keep the default values as they are. Setting both parameters too low will result in the client receiving failed VM submission responses.


Similarly to dispatching two parameters exist to control the response gather behavior: numberOfRetries and retryInterval. The number of retries parameter makes sure that polling terminates in case when the responses never become available due to internal errors while the retry interval specifies the polling period.


* *submission.packingDensity.*

 You can define a packing density for each resource. It will be considered during initial VM placement and allow the VM to be hosted on a LC despite existing MID capping. For example, of a LC has 4 PCORES and the CPU MID threshold (monitoring.thresholds.cpu) is set to 0.5 it is only possible to load it for up to 2 PCORES, keeping two other cores as buffered capacity. If a VM is now submitted which requires 4 VCORES it impossible to place it on the LC. However, with the packing density set to 0.4 it will be considered as a VM requiring only 1.6 VCORES thus allowing it to be placed. Note that packing density < 1 facilitates resource overcommit and thus can lead to serious performance problems. We suggest to keep it at the default value (=1) in case performance is important.


Energy Management
^^^^^^^^^^^^^^^^^

* *energyManagement.enabled*

Enables/disables the energy saving module. Once enabled it will periodically observe the LCs and transitioned idle (= not hosting any VMs) LCs into a lower power-state.

* *energyManagement.numberOfReservedNodes*

You can set the number of reserved nodes using this parameter. In case of a positive value the GM will keep the predefined number of LCs always online. 

* *energyManagement.powerSavingAction*

 In case the decision has been made to transition an idle LC into a lower power state the power saving action will be triggered. You can choose between: shutdown, suspendToRam, suspendToDisk, and suspendToBoth. Note that you have to make sure that your hardware supports the selected action.

* *energyManagement.drivers.* 

 You can choose different drivers to shutdown and wake up the system. For example, shutdown can be achieved using the native system shutdown command or IPMI. Similarly, various Linux scripts exist to trigger suspend actions (e.g. pmutils, uswsusp). Finally, a several wake up methods such as IPMI and Wake-On-Lan (WOL) exist with each of them requiring different arguments. You can use the options parameter to pass additional arguments (e.g. MAC address for WOL or authentication data for IPMI).

You can select the driver you think is supported in your environment or implement your own driver by implementing the appropriate driver interface (see Developers documentation for more details). Note that you have to configure your system properly by installing the appropriate tools required by the drivers and give them enough system access rights to operate correctly. Please see the Deployment documentation for more details.


* *energyManagement.thresholds.idleTime*

The time interval in seconds used to observe the LC load (e.g. every 120 seconds). 

* *energyManagement.thresholds.wakeupTime*

The time to wait in seconds until a LC is considered active. Note that it is crucial for the proper functioning of the system to set this interval as accurate as possible. For example, if your system needs approximately 5 minutes to come back online (which is actually the right value for some modern Blade HP servers) after being shutdown, set wake up time to 300 seconds!

* *energyManagement.commandExecutionTimeout*

Sometimes the implemented drivers commands can be fragile and block the system if they do not terminate correctly. You can use the command execution timeout to force termination after a predefined number of seconds. Note that it is important to keep this value high enough in order to prevent situations where driver commands are aborted too early.


