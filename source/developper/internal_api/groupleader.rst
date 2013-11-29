Group Leader 
-----------------------

========================== =
Methods Summary
========================== =
startVirtualCluster_
getVirtualClusterResponse_
migrateVirtualMachine_
getLocalControllerList_
========================== =


In this section we assume that the group leader node is running on localhost.

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


Using it
********

* Curl

::

      ($) curl -H "Content-Type: application/json" -X POST -d "$template" http://localhost:5001/groupmanager?startVirtualCluster

::

      a4fba1c6-4c8f-4691-a530-01c067e8deb2


* Java 

::

        // Gets the leader address.
        NetworkAddress bootstrapAddress = new NetworkAddress();
        bootstrapAddress.setAddress("localhost");
        bootstrapAddress.setPort(5000);
        BootstrapAPI bootstrapCommunicator = CommunicatorFactory.newBootstrapCommunicator(bootstrapAddress);
        GroupManagerDescription groupLeaderDescription = bootstrapCommunicator.getGroupLeaderDescription();
 
        // Construct the virtual machine templates array.
        VirtualClusterSubmissionRequest virtualClusterDescription = new VirtualClusterSubmissionRequest();
        ArrayList<VirtualMachineTemplate> virtualMachineTemplates = new ArrayList<VirtualMachineTemplate>();
        VirtualMachineTemplate virtualMachineTemplate = new VirtualMachineTemplate();
        virtualMachineTemplate.setLibVirtTemplate("[...]");
        NetworkDemand networkDemand = new NetworkDemand();
        networkDemand.setRxBytes(12800);
        networkDemand.setTxBytes(12800);
        virtualMachineTemplate.setNetworkCapacityDemand(networkDemand);
        
        virtualMachineTemplates.add(virtualMachineTemplate);        
        virtualClusterDescription.setVirtualMachineTemplates(virtualMachineTemplates);
        
        // Call to the startVirtualCluster Method. 
        NetworkAddress groupLeaderAddress = groupLeaderDescription.getListenSettings().getControlDataAddress();
        GroupManagerAPI groupLeaderCommunicator = CommunicatorFactory.newGroupManagerCommunicator(groupLeaderAddress);
        String taskIdentifier = groupLeaderCommunicator.startVirtualCluster(virtualClusterDescription);


* Python  (libcloud)

When using this driver, polling for the response is wrapped in the *create_node* method of the driver.

::

    >> from libcloud.compute.types import Provider
    >> from libcloud.compute.providers import get_driver
    >> Snooze = get_driver(Provider.SNOOZE)
    >> driver = Snooze("127.0.0.1","5000")

    >> driver.create_node(libvirt_templates=["/home/msimonin/Images-VM/Snooze-images/vmtemplates/debian1.xml"])

    Out << [<Node: uuid=b69042832fd33b092eb5d0211554d3c983c80ddf, name=debian1, state=RUNNING, public_ips=192.168.122.11, provider=Snooze ...>]

.. _getvirtualclusterresponse:

getVirtualClusterResponse
#########################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request that must contain a string identifying the cluster submission request.

Return
******

The virtual cluster response or empty if the response isn't available yet.

Using it
********


* Curl

:: 

      ($) curl  -H "Content-Type: application/json" -X POST -d "a4fba1c6-4c8f-4691-a530-01c067e8deb2" http://localhost:5001/groupmanager?getVirtualClusterResponse

::

      {
         "errorCode":null,
         "virtualMachineMetaData":[
            {
               "status":"RUNNING",
               "virtualMachineLocation":{
                  "virtualMachineId":"debian1",
                  "localControllerId":"ddb07acb-643e-4f9d-87e3-23ae4b629509",
                  "groupManagerId":"20fb798c-5d72-47d7-b80e-a613a81dc603",
                  "groupManagerControlDataAddress":{
                     "address":"127.0.0.1",
                     "port":5002
                  },
                  "localControllerControlDataAddress":{
                     "address":"127.0.0.1",
                     "port":5003
                  }
               },
               "usedCapacity":{

               },
               "requestedCapacity":[
                  1.0,
                  200000.0,
                  12800.0,
                  12800.0
               ],
               "ipAddress":"192.168.122.6",
               "errorCode":"UNKNOWN",
               "groupManagerControlDataAddress":{
                  "address":"127.0.0.1",
                  "port":5002
               },
               "xmlRepresentation":"<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\"?><domain type=\"kvm\">  <name>debian1</name>  <uuid>0f476e56-67ea-11e1-858e-00216a972a36</uuid>  <memory>200000</memory>  <currentMemory>200000</currentMemory>  <vcpu>1</vcpu>  <os>    <type arch=\"x86_64\" machine=\"pc-0.12\">hvm</type>    <boot dev=\"hd\"/>  </os>  <features>    <acpi/>    <apic/>    <pae/>  </features>  <clock offset=\"utc\"/>  <on_poweroff>destroy</on_poweroff>  <on_reboot>restart</on_reboot>  <on_crash>restart</on_crash>  <devices>    <emulator>/usr/bin/kvm</emulator>    <disk device=\"disk\" type=\"file\">      <driver name=\"qemu\" type=\"qcow2\"/>      <source file=\"/home/msimonin/Images-VM/Snooze-images/imgs/debian1.qcow2\"/>      <target bus=\"virtio\" dev=\"vda\"/>      <address bus=\"0x00\" domain=\"0x0000\" function=\"0x0\" slot=\"0x05\" type=\"pci\"/>    </disk>    <controller index=\"0\" type=\"ide\">      <address bus=\"0x00\" domain=\"0x0000\" function=\"0x1\" slot=\"0x01\" type=\"pci\"/>    </controller>    <interface type=\"bridge\">      <mac address=\"54:56:c0:a8:7a:6\"/>      <source bridge=\"virbr0\"/>    </interface>    <serial type=\"pty\">      <target port=\"0\"/>    </serial>    <console type=\"pty\">      <target port=\"0\" type=\"serial\"/>    </console>    <graphics autoport=\"yes\" listen=\"0.0.0.0\" port=\"-1\" type=\"vnc\"/>    <input bus=\"usb\" type=\"tablet\"/>    <input bus=\"ps2\" type=\"mouse\"/>    <memballoon model=\"virtio\">      <address bus=\"0x00\" domain=\"0x0000\" function=\"0x0\" slot=\"0x06\" type=\"pci\"/>    </memballoon>  </devices></domain>"
            }
         ]
      }

* Java

::
   
    // This code can follow the code from above.
    GroupManagerAPI groupLeaderCommunicator = CommunicatorFactory.newGroupManagerCommunicator(networkAddress);
    virtualClusterResponse = groupLeaderCommunicator.getVirtualClusterResponse(taskIdentifier);


.. _migratevirtualmachine:

migrateVirtualMachine
#####################

Method
******

POST

Input Data
**********
  You must provide a JSON encoded hash in the body of your request that must contain the following parameters : 
  
  * sourceVirtualMachineLocation : 

  * destinationVirtualMachineLocation :

  * destinationHypervisorSettings : can be left empty

  * migrated : set to False

Return 
******

Using it :
**********

* Python (libcloud)


::


      from libcloud.compute.types import Provider
      from libcloud.compute.providers import get_driver
      Snooze = get_driver(Provider.SNOOZE)
      driver = Snooze("127.0.0.1","5000");

      n1 = driver.create_node(libvirt_templates=["/home/msimonin/Images-VM/Snooze-images/vmtemplates/debian1.xml"],
                         tx=12800,
                         rx=12800
                        )
      n2 = driver.create_node(libvirt_templates=["/home/msimonin/Images-VM/Snooze-images/vmtemplates/debian2.xml"],
                         tx=12800,
                         rx=12800
                        )


      driver.migrate(n1[0],n2[0].extra.get("virtualMachineLocation"))



.. _getlocalcontrollerlist:

getLocalControllerList
#####################

Method
******

GET 

Return 
******

JSON encoded Local controller list (see below).

Using it :
**********

* Curl

::

    curl localhost:5000/bootstrap?getLocalControllerList

::

   {
      "localControllers":[
         {
            "id":"f5a4a86a-e83d-4508-9ca5-ff8f5d3724e3",
            "controlDataAddress":{
               "address":"127.0.0.1",
               "port":5005
            },
            "status":"ACTIVE",
            "hypervisorSettings":{
               "port":16511,
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
            "virtualMachineMetaData":{

            },
            "wakeupSettings":{
               "driver":"IPMI",
               "options":"-I lanplus -H BMC_IP -U user -P password"
            },
            "hostname":"mafalda",
            "assignedVirtualMachines":[

            ]
         },
         {
            "id":"496de94b-bc00-4105-8c2d-dba3797e6a2a",
            "controlDataAddress":{
               "address":"127.0.0.1",
               "port":5004
            },
            "status":"ACTIVE",
            "hypervisorSettings":{
               "port":16510,
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
            "virtualMachineMetaData":{

            },
            "wakeupSettings":{
               "driver":"IPMI",
               "options":"-I lanplus -H BMC_IP -U user -P password"
            },
            "hostname":"mafalda",
            "assignedVirtualMachines":[

            ]
         },
         {
            "id":"9d908d25-b6af-4e3c-bd71-ca6ac290ecf6",
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
            "virtualMachineMetaData":{

            },
            "wakeupSettings":{
               "driver":"IPMI",
               "options":"-I lanplus -H BMC_IP -U user -P password"
            },
            "hostname":"mafalda",
            "assignedVirtualMachines":[

            ]
         }
      ]
   }


