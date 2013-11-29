Bootstrap 
--------------------

================================= =
Methods Summary
================================= =
destroyVirtualMachine_
getLocalControllerList_
getGroupLeaderDescription_
getCompleteHierarchy_
getGroupManagerDescriptions_
getGroupManagerDescription_
getLocalControllerDescriptions_
getVirtualMachinesDescriptions_
migrateVirtualMachine_
rebootVirtualMachine_
resumeVirtualMachine_
shutdownVirtualMachine_
startReconfiguration_
startVirtualCluster_
suspendVirtualMachine_
================================= =


In this section we assume that a *bootstrap* node is running on *localhost*.

.. _destroyvirtualmachine:

destroyVirtualMachine
######################

Method
******

POST

Input Data
**********

The virtual machine identifier.

Returns
*******

boolean.

.. _getlocalcontrollerlist:

getLocalControllerList
######################

Method
******

GET

Returns
*******

The local controller list

.. _getgroupleaderdescription:

getGroupLeaderDescription
#########################

Method
******

GET

Returns
*******

The group manager description object of the group leader.


Using it
********

* Curl

::

    ($) curl localhost:5000/bootstrap?getGroupLeaderDescription

::

    {
      "id":"f5cfb585-800e-4ff8-a97f-5681d0610892",
      "hostname":"mafalda",
      "listenSettings":{
         "controlDataAddress":{
            "address":"127.0.0.1",
            "port":5001
         },
         "monitoringDataAddress":{
            "address":"127.0.0.1",
            "port":6000
         }
      },
      "localControllers":{
      },
      "heartbeatAddress":{
         "address":"UNKNOWN",
         "port":-1
      },
      "summaryInformation":{
      },
      "virtualMachines":[
      ]
    }


* Java 

::


    import org.inria.myriads.snoozecommon.communication.NetworkAddress;
    import org.inria.myriads.snoozecommon.communication.groupmanager.GroupManagerDescription;
    import org.inria.myriads.snoozecommon.communication.rest.CommunicatorFactory;
    import org.inria.myriads.snoozecommon.communication.rest.api.BootstrapAPI;

    [...]
    
    NetworkAddress bootstrapAddress = new NetworkAddress();
    bootstrapAddress.setAddress("localhost");
    bootstrapAddress.setPort(5000);
    BootstrapAPI bootstrapCommunicator = CommunicatorFactory.newBootstrapCommunicator(bootstrapAddress);
    GroupManagerDescription groupLeaderDescription = bootstrapCommunicator.getGroupLeaderDescription();

* Python (libcloud)

::

    >> from libcloud.compute.types import Provider
    >> from libcloud.compute.providers import get_driver

    >> Snooze = get_driver(Provider.SNOOZE)
    >> driver = Snooze("127.0.0.1","5000")

    >> data = driver.get_groupleader()

    >> print data

::

   {
      "summaryInformation": {}, 
      "localControllers": {}, 
      "hostname": "mafalda", 
      "heartbeatAddress": {
         "port": -1, 
         "address": "UNKNOWN"
      }, 
      "virtualMachines": [], 
      "listenSettings": {
         "controlDataAddress": {
            "port": 5001, 
            "address": "127.0.0.1"
         }, 
         "monitoringDataAddress": {
            "port": 6000, 
            "address": "127.0.0.1"
         }
      }, 
      "id": "f5cfb585-800e-4ff8-a97f-5681d0610892"
   }

.. _getcompletehierarchy:

getCompleteHierarchy
####################

Method
******

GET

Returns
*******

The complete hierarchy of the systems.

Using it
********

* Curl  (2 GMs - 1 LC)

::

    ($) curl http://localhost:5000/bootstrap?getCompleteHierarchy

:: 

    {
      "groupManagerDescriptions":[
         {
            "id":"20fb798c-5d72-47d7-b80e-a613a81dc603",
            "listenSettings":{
               "controlDataAddress":{
                  "address":"127.0.0.1",
                  "port":5002
               },
               "monitoringDataAddress":{
                  "address":"127.0.0.1",
                  "port":6001
               }
            },
            "localControllers":{
               "ddb07acb-643e-4f9d-87e3-23ae4b629509":{
                  "id":"ddb07acb-643e-4f9d-87e3-23ae4b629509",
                  "controlDataAddress":{
                     "address":"127.0.0.1",
                     "port":5003
                  },
                  "status":"ACTIVE",
                  "hypervisorSettings":{
                     "port":16509,
                     "driver":"qemu",
                     "transport":"tcp",
                     "migration":{
                        "method":"live",
                        "timeout":60
                     }
                  },
                  "totalCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "wakeupSettings":{
                     "driver":"IPMI",
                     "options":"-I lanplus -H BMC_IP -U user -P password"
                  },
                  "hostname":"mafalda",
                  "virtualMachineMetaData":{

                  },
                  "assignedVirtualMachines":[

                  ]
               }
            },
            "heartbeatAddress":{
               "address":"225.4.5.6",
               "port":10001
            },
            "hostname":"mafalda",
            "summaryInformation":{
               "1373888959514":{
                  "timeStamp":1373888959514,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888956509":{
                  "timeStamp":1373888956509,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888953504":{
                  "timeStamp":1373888953504,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888950498":{
                  "timeStamp":1373888950498,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888947493":{
                  "timeStamp":1373888947493,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888944487":{
                  "timeStamp":1373888944487,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888941482":{
                  "timeStamp":1373888941482,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888938477":{
                  "timeStamp":1373888938477,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888935472":{
                  "timeStamp":1373888935472,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               },
               "1373888932467":{
                  "timeStamp":1373888932467,
                  "usedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "requestedCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "localControllers":[

                  ],
                  "activeCapacity":[
                     4.0,
                     3958348.0,
                     131072.0,
                     131072.0
                  ],
                  "passiveCapacity":[
                     0.0,
                     0.0,
                     0.0,
                     0.0
                  ],
                  "legacyIpAddresses":[

                  ]
               }
            },
            "virtualMachines":[

            ]
         }
      ]
    }

* Java

:: 

    import org.inria.myriads.snoozecommon.communication.NetworkAddress;
    import org.inria.myriads.snoozecommon.communication.groupmanager.repository.GroupLeaderRepositoryInformation;
    import org.inria.myriads.snoozecommon.communication.rest.CommunicatorFactory;
    import org.inria.myriads.snoozecommon.communication.rest.api.BootstrapAPI;

    [...]

    NetworkAddress bootstrapAddress = new NetworkAddress();
    bootstrapAddress.setAddress("localhost");
    bootstrapAddress.setPort(5000);
    BootstrapAPI bootstrapCommunicator = CommunicatorFactory.newBootstrapCommunicator(bootstrapAddress);
    GroupLeaderRepositoryInformation hierarchy = bootstrapCommunicator.getCompleteHierarchy();

* Python (libcloud)

Not implemented

.. _getgroupmanagerdescriptions:

getGroupManagerDescriptions
############################

Method
*******

POST

Input data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the HostListRequest java object. 


Returns
*******

The list of group managers matching the request.

.. _getgroupmanagerdescription:

getGroupManagerDescription
##########################

Method
******

POST

Input Data
**********

The group manager identifier.

Returns
*******

The group manager description.


.. _getlocalcontrollerdescriptions:

getLocalControllerDescriptions
##############################

Method
*******

POST

Input data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the HostListRequest java object. 


Returns
*******

The list of group managers matching the request.


.. _getvirtualmachinedescriptions:

getVirtualMachinesDescriptions
##############################

Method
*******

POST

Input data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the HostListRequest java object. 


Returns
*******

The list of virtual machines matching the request.

.. _migrateVirtualMachine:

migrateVirtualMachine
#####################

Method
*******

POST

Input data
**********

You must provide a JSON encoded hash in the body of your request which\
correspond to the ClientMigrationRequestSimple java object. 

Returns
*******

boolean

.. _rebootvirtualmachine:

rebootVirtualMachine
#####################

Method
*******

POST

Input data
**********

The virtual machine identifier.

Returns
*******

boolean

.. _resumevirtualmachine:

resumeVirtualMachine
#####################

Method
*******

POST

Input data
**********

The virtual machine identifier.

Returns
*******

boolean

.. _shutdownvirtualmachine:

shutdownVirtualMachine
######################

Method
*******

POST

Input data
**********

The virtual machine identifier.

Returns
*******

boolean

.. _startreconfiguration:

startReconfiguration
######################

Method
*******

POST

Input data
**********

The groupmanager identifier.

Returns
*******

boolean

.. _startvirtualcluster:

startVirtualCluster
###################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request that must contain a parameter *virtualMachineTemplates* which value is an array of virtualMachineTemplate.

virtualMachineTemplate must contain the following parameters:

* libVirtTemplate : xml string representing the virtual machine to start

* networkCapacityDemand : hash which parameters are rxBytes and txBytes.

A example is given below with one virtual machine: 

::

      {
         "virtualMachineTemplates":[
            {
               "libVirtTemplate":"<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\"?><domain type=\"kvm\"> <name>debian1</name>  <uuid>0f476e56-67ea-11e1-858e-00216a972a36</uuid>  <memory>200000</memory>  <currentMemory>200000</currentMemory>  <vcpu>1</vcpu>  <os>    <type arch=\"x86_64\" machine=\"pc-0.12\">hvm</type>    <boot dev=\"hd\"/>  </os>  <features>    <acpi/>    <apic/>    <pae/>  </features>  <clock offset=\"utc\"/>  <on_poweroff>destroy</on_poweroff>  <on_reboot>restart</on_reboot>  <on_crash>restart</on_crash>  <devices>    <emulator>/usr/bin/kvm</emulator>    <disk device=\"disk\" type=\"file\">      <driver name=\"qemu\" type=\"qcow2\"/>      <source file=\"path_to_disk_image\"/>      <target bus=\"virtio\" dev=\"vda\"/>      <address bus=\"0x00\" domain=\"0x0000\" function=\"0x0\" slot=\"0x05\" type=\"pci\"/>    </disk>    <controller index=\"0\" type=\"ide\">      <address bus=\"0x00\" domain=\"0x0000\" function=\"0x1\" slot=\"0x01\" type=\"pci\"/>    </controller>    <interface type=\"bridge\">      <mac address=\"52:54:01:84:26:2c\"/>      <source bridge=\"virbr0\"/>    </interface>    <serial type=\"pty\">      <target port=\"0\"/>    </serial>    <console type=\"pty\">      <target port=\"0\" type=\"serial\"/>    </console>    <graphics autoport=\"yes\" listen=\"0.0.0.0\" port=\"-1\" type=\"vnc\"/>    <input bus=\"usb\" type=\"tablet\"/>    <input bus=\"ps2\" type=\"mouse\"/>    <memballoon model=\"virtio\">      <address bus=\"0x00\" domain=\"0x0000\" function=\"0x0\" slot=\"0x06\" type=\"pci\"/>    </memballoon>  </devices> </domain>",
               "networkCapacityDemand":{
                  "rxBytes":12800.0,
                  "txBytes":12800.0
               }
            }
         ]
      }


Return
******

The assigned task identifier.

.. _suspendvirtualmachine:

suspendVirtualMachine
#####################

Method
*******

POST

Input data
**********

The virtual machine identifier.

Returns
*******

boolean.
