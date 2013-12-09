SnoozeEC2 Debian package
-------------------------

Installation
^^^^^^^^^^^^

Get the debian package from the website and install it with dpkg:

::

    dpkg -i snoozeimage_X.X.X.deb


Files
^^^^^

The snoozeec2 files reside in */usr/share/snoozeec2* 
and the init script in */etc/init.d/snoozeec2*.


Run the service
^^^^^^^^^^^^^^^

* You can launch the snoozeec2 daemon with  :

::
 
    service snoozeec2 start

It will takes default the logger configuration file in 
*/usr/share/snoozeec2/configs/log4j.xml* and logs will be printed in */tmp/snooze_ec2.log*.

* If you prefer running the service manually, you can invoke : 

::

    java -jar path_to_snoozeec2_jar cfgFile logFile

The snoozeec2 jar can be found in /usr/share/snoozeec2/
