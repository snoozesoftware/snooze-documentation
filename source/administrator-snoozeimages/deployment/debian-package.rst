Debian package
--------------

Installation
^^^^^^^^^^^^

Get the debian package from the website and install it with dpkg:

::

    dpkg -i snoozeimage_X.X.X.deb


Files
^^^^^

The snoozeimages files reside in */usr/share/snoozeimages* 
and the init script in */etc/init.d/snoozeimages*.


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
