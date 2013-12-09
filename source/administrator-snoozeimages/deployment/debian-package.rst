.. _snoozeimages_deb:

SnoozeImages Debian package
-----------------------------

Installation
^^^^^^^^^^^^

Get the debian package from the website and install it with dpkg:

::

    dpkg -i snoozeimage_X.X.X.deb

Install also libvirt.

::

  apt-get install libvirt



Libvirt pool Backend
^^^^^^^^^^^^^^^^^^^^^

Snoozeimages can be configured to use libvirt pool as a storage backend. 
Thus you need to configure a libvirt instance in order for the snoozeimages
service to connnect to.

Configure a pool to serve virtual machine images
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Usually, a *default* pool exists once libvirt is install. You can check by typing : 


::

  virsh pool-list --all

Make sure it is activated.

::

  virsh pool-start [pool-name]

If no pool exists, create one.

::

  virsh pool-define-as --name [pool-name] --target /var/lib/libvirt/images --type dir
  virsh pool-build [pool-name]
  virsh pool-start [pool-name]

Configure snoozeimages to connect to libvirt
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Configuration file can be found in */usr/share/snoozeimages/configs*.

Once the pool is started, fill the config */usr/share/snoozeimages/configs/snooze_images.cfg* with the appropriate pool name.


Run the service
^^^^^^^^^^^^^^^

* Run the libvirt instance.

* Launch the snoozeimages daemon with  :

::
 
    service snoozeimages start

It will takes default the logger configuration file in 
*/usr/share/snoozeimages/configs/log4j.xml* and logs will be printed in */tmp/snooze_images.log*.

* If you prefer running the service manually, you can invoke : 

::

    java -jar path_to_snoozeimages_jar cfgFile logFile

The snoozeimages jar can be found in /usr/share/snoozeimages/


Check your deployment
^^^^^^^^^^^^^^^^^^^^^

In order to make sure that the snoozeimages is running properly you can check the following : 

* log file, by default located in /tmp/snoozeimages.log
* submit a *curl* command to the service : 

::

  curl localhost:4000/images
