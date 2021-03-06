
.. _rabbitmq-message-format:

RabbitMQ message format
-----------------------

Snooze sent notifications messages over 3 topic exchanges : 

* *system* exchange for messages concerning the system itself
* *management* exchange for messages concerning the virtual machines management
* *monitoring* exchange for monitoring messages (unused for the moment)

All message are JSON formatted.

System messages
^^^^^^^^^^^^^^^

The template for this kind of message is 

::

  {
    "message"   : MESSAGE 
    "eventType" : TYPE
  }

The routing key depends on the sender. Below, you will find an example of such a message send by the leader once elected with the routing key *groupleader*. Basically the message is an instance of the GroupManagerDescription class reprepresenting the leader.

::

   {
      "message":{
         "id":"518486ac-df5a-4e16-bfb9-238cbf2a9038",
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

         },
         "heartbeatAddress":{
            "address":"225.4.5.6",
            "port":10001
         },
         "hostname":"mafalda",
         "summaryInformation":{

         },
         "virtualMachines":[

         ]
      },
      "eventType":"GL_JOIN"
   }


============ =================== =============== ===========================
sender       routing key         eventType       message
============ =================== =============== ===========================
groupleader  "groupleader"       GL_JOIN         GroupManagerDescription
groupleader  "groupleader"       GM_JOIN         GroupManagerDescription
groupleader  "groupleader"       GM_FAILED       GroupManagerId
groupmanager "groupmanager".gmid LC_JOIN         LocalControllerDescription
groupmanager "groupmanager".gmid LC_FAILED       LocalControllerId
groupmanager "groupmanager".gmid LC_ANOMALY      LocalcontrollerDescription
groupmanager "groupmanager".gmid RECONFIGURATION ReconfigurationPlan
============ =================== =============== ===========================



Management messages
^^^^^^^^^^^^^^^^^^^

The template for this kind of message is : 

::

  {
    "message" : MESSAGE,
    "status"  : STATUS
  }

The status can be either PENDING, ERROR, PROCESSED.
The routing key depends on the sender, the virtual machine and the action it can be summarized as follow : 

::

  gmid.lcid.vmid.action

Below an example of a message received after a start request. The virtual machine *debian1* is hosted by localcontroller *ca0b0e33-00e3-41f3-a301-0997ba614880* managed by groupmanager *acc855f9-6c44-4413-b297-62c9c7c0b181* so the routing key was :  

::

  acc855f9-6c44-4413-b297-62c9c7c0b181.ca0b0e33-00e3-41f3-a301-0997ba614880.debian1.START

and the body of the message was an instance of VirtualMachineMetaData.
  
::

   {
      "message":{
         "status":"RUNNING",
         "virtualMachineLocation":{
            "virtualMachineId":"debian1",
            "localControllerId":"ca0b0e33-00e3-41f3-a301-0997ba614880",
            "groupManagerId":"acc855f9-6c44-4413-b297-62c9c7c0b181",
            "groupManagerControlDataAddress":{
               "address":"127.0.0.1",
               "port":5001
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
            128000.0,
            12800.0,
            12800.0
         ],
         "ipAddress":"192.168.122.2",
         "errorCode":"UNKNOWN",
         "groupManagerControlDataAddress":{
            "address":"127.0.0.1",
            "port":5001
         },
         "xmlRepresentation":"[...]"
      },
      "status":"PROCESSED"
   }

======================= ======================
routing key             Message
======================= ======================
gmid.lcid.vmid.START    VirtualMachineMetaData
gmid.lcid.vmid.SUSPEND  VirtualMachineMetaData
gmid.lcid.vmid.RESUME   VirtualMachineMetaData
gmid.lcid.vmid.SHUTDOWN VirtualMachineMetaData
gmid.lcid.vmid.DESTROY  VirtualMachineMetaData
gmid.lcid.vmid.MIGRATE  VirtualMachineMetaData
======================= ======================
