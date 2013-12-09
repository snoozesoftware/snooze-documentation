SnoozeEC2 Config File
------------------------

Snoozeec2 system  service configuration is done using its main configuration file /usr/share/snoozeec2/configs/snooze_ec2.cfg.

Networking
^^^^^^^^^^

* *network.listen.address*

Binds the Snoozeec2 service to the specified address. By default it listen on all interfaces.

* *network.listen.controlDataPort* 

Control data port is required for the REST communication on each node.


Images Repository
^^^^^^^^^^^^^^^^^^

* *imageRepository.address* 

The address of one snoozeimages system service.

* *imageRepository.port* 

The port which the snoozeimages repository service is listening on.


Bootstraps
^^^^^^^^^^

* *bootstrap.address* 

The address of one bootstrap node.

* *bootstrap.port*

The port which the bootstrap node is listening on.
