Snoozeimages Config File
------------------------

Snoozeimages system  service configuration is done using its main configuration file /usr/share/snoozeimages/configs/snooze_image.cfg.



Networking
^^^^^^^^^^

* *network.listen.address*

Binds the Snoozeimages service to the specified address. By default it listen on all interfaces.

* *network.listen.controlDataPort* 

Control data port is required for the REST communication on each node.

Repository Backend
^^^^^^^^^^^^^^^^^^^

* *repository.type* 

  This option corresponds to the backend type used to serve images. Only *libvirt* is supported for the moment.
  Images will be served through the libvirt pool implementation. Note that the pool referenced by the *repository.libvirt.pool* option must be created and accessible before starting the snoozeimages system service.

* *repository.libvirt.address* 

  Address of the libvirt backend to use.


* *repository.libvirt.port* 

  Port of the libvirt backend to use.

* *repository.libvirt.hypervisor* 

  Hypervisor to use.

* *repository.libvirt.transport* 

  transport protocol to use.

* *repository.libvirt.pool* 

  Libvirt pool to use as images repository backend.


