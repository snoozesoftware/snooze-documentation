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


Files
^^^^^

The snoozeimages files reside in */usr/share/snoozeimages* 
and the init script in */etc/init.d/snoozeimages*.

Configure a pool to serve virtual machine images
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

Once the pool is started, fill the config */usr/share/snoozeimages/configs/snooze_images.cfg* with the appropriate pool name.

Run the service
^^^^^^^^^^^^^^^

* You can launch the snoozeimages daemon with  :

::
 
    service snoozeimages start

It will takes default the logger configuration file in 
*/usr/share/snoozeimages/configs/log4j.xml* and logs will be printed in */tmp/snooze_images.log*.

* If you prefer running the service manually, you can invoke : 

::

    java -jar path_to_snoozeimages_jar cfgFile logFile

The snoozeimages jar can be found in /usr/share/snoozeimages/
