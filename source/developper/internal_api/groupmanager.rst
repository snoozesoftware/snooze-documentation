Group Manager
------------------------

========================== =================
External Methods            Internal Methods
========================== =================
destroyVirtualMachine_
rebootVirtualMachine_
resumeVirtualMachine_
shutdownVirtualMachine_
suspendVirtualMachine_
========================== =================


In this section we assume that the group leader node is running on localhost.

.. _destroyVirtualMachine:

destroyVirtualMachine
#####################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the VirtualMachineMachineLocation java object.


Return
******

boolean.

Using it
********

* Python  (libcloud)

The mechanism of getting the location of the virtual machine is wrapped in the call.


::

    >> from libcloud.compute.types import Provider
    >> from libcloud.compute.providers import get_driver
    >> Snooze = get_driver(Provider.SNOOZE)
    >> driver = Snooze("127.0.0.1","5000")

    >> l = driver.create_node(libvirt_templates=["/home/msimonin/Images-VM/Snooze-images/vmtemplates/debian1.xml"])

    Out << [<Node: uuid=b69042832fd33b092eb5d0211554d3c983c80ddf, name=debian1, state=RUNNING, public_ips=192.168.122.11, provider=Snooze ...>]

    >> driver.destroy(l[0])

.. _rebootVirtualMachine:

rebootVirtualMachine
#####################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the VirtualMachineMachineLocation java object.

::

Return
******

boolean.

Using it
********

* Python  (libcloud)

::

    >> driver.reboot(l[0])

.. _resumeVirtualMachine:

resumeVirtualMachine
#####################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the VirtualMachineMachineLocation java object.


Return
******

boolean.

Using it
********

* Python  (libcloud)

::

    >> driver.resume(l[0])

.. _shutdownVirtualMachine:

shutdownVirtualMachine
#######################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the VirtualMachineMachineLocation java object.


Return
******

boolean.

Using it
********

* Python  (libcloud)

::

    >> driver.shutdown(l[0])

.. _suspendVirtualMachine:

suspendVirtualMachine
#####################

Method
******

POST

Input Data
**********

You must provide a JSON encoded hash in the body of your request which correspond to the VirtualMachineMachineLocation java object.


Return
******

boolean.

Using it
********

* Python  (libcloud)

::

    >> driver.suspend(l[0])
