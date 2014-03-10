.. _Downloads: http://snooze.inria.fr/download/
.. _GitDeployLocalCluster: http://github.com/snoozesoftware/snooze-deploy-localcluster/

Single Machine Setup
--------------------

Local deployment involves running all the Snooze system components: Bootstrap Node (BN), Group Leader (GL), Group Managers (GMs), Local Controller (LCs), Snoozeimages and SnoozeEC2,  as well as dependencies: Apache ZooKeeper, libvirtd ( and optionnally apache Cassandra and Rabbitmq) on the same machine (e.g. your laptop). 


Hardware and OS requirement
^^^^^^^^^^^^^^^^^^^^^^^^^^^
* Networking connection for multicast
* Hardware supporting the KVM hypervisor 
* Debian wheezy or Ubuntu (12.04 or newer) 

Note that snooze is written in java, so any linux flavour should be able to run the system.

Prepare the environment
^^^^^^^^^^^^^^^^^^^^^^^

Install the Java runtime environment, Apache ZooKeeper, and QEMU/KVM.

:: 

  $ apt-get install openjdk-7-jre zookeeper zookeeperd qemu-kvm libvirt-bin libvirt-dev
  $ groupadd snooze
  $ useradd -s /bin/false snoozeadmin -g snooze

Add User *snoozeadmin* the libvirt group.

::

  (Debian)$ adduser snoozeadmin libvirt
  (Ubuntu)$ adduser snoozeadmin libvirtd

Install Snooze system packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the latest Snooze client and node Debian packages from the Downloads_ page and install them.

::

  dpkg -i snoozenode_X.X-X_all.deb
  dpkg -i snoozeimages_X.X-X_all.deb
  dpkg -i snoozeec2_X.X-X_all.deb


Configure snoozeimages according to :ref:`snoozeimages_deb` instructions.

Note that alternatively you can install the jar files from the release download page.

Use the local deployment script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The local deployment script is made to facilitative the start of a local cluster. Particularly, it automatically creates all the configuration files needed to run multiple Snooze and libvirt daemons on a single machine. Moreover it launches the daemons and the required dependencies (e.g. the Apache ZooKeeper service).

You can get the local deployment script from the Downloads_ page. Unpack the archive and configure your Snooze deployment scenario. In other words, you need to inform the script how many BNs, GMs, and LCs you would like to run on your host. Therefore open the *./scripts/settings.sh* file in localcluster subdirectory.

First we will check the paths to the binaries and the config file : 

::

  install_directory="/usr/share/snoozenode"
  node_jar_file="$install_directory/snoozenode.jar"
  node_config_file="$install_directory/configs/snooze_node.cfg"
  node_log_file="$install_directory/configs/log4j.xml"

:: 

  snoozeimages_install_directory="/usr/share/snoozeimages"
  snoozeimages_jar_file="$snoozeimages_install_directory/snoozeimages.jar"
  snoozeimages_config_file="$snoozeimages_install_directory/configs/snooze_images.cfg"
  snoozeimages_log_file="$snoozeimages_install_directory/configs/log4j.xml"

Then we will configure the deployment scenario :

::

  number_of_bootstrap_nodes=1
  number_of_group_managers=2
  number_of_local_controllers=1

Adapt them to your needs and you are almost done. You are now ready to instruct the script to launch the libvirtd and Snooze daemons. Note that in case you already have a libvirt and snoozenode daemon running (default installation), they needs to be stopped to avoid possible port collisions.


::

  $ /etc/init.d/libvirt-bin stop
  $ /etc/init.d/snoozenode stop


To list the available local deployment script options start the following command

::

  $ ./start_local_cluster.sh


::

  Options:
  -l Start libvirt
  -d Stop libvirt
  -s Start cluster
  -k Kill cluster

Run the script with “-l” and “-s” options to start the libvirt and Snooze daemons

::

  $ ./start_local_cluster.sh -l
  $ ./start_local_cluster.sh -s


You now should have your local Snooze cluster up and running. The debug outputs of the Snooze components are stored in the /tmp/ directory under the file names: snooze_node_bn.log, snooze_node_gm1.log, etc. In case you need to stop the cluster use the “-d” and “-k” options.


Check your deployment
^^^^^^^^^^^^^^^^^^^^^
* First of all, logs files should have been created in */tmp/snooze\*.log*.
  You can check the content of these files
 
* Test your snoozeimages installation by submitting a  *curl* command : 

::

  curl localhost:4000/images

It should return the list of the images in the pool configured in snoozeimages configuration file.

How to change parameters
^^^^^^^^^^^^^^^^^^^^^^^^

If you want to change some parameters in the configuration file open the file pointed by *$snoozeimages_config_file*. By default it should be */usr/share/snoozenode/configs/snooze_node.cfg*.
Make your changes and apply them by restarting the cluster.

::

  $ ./start_local_cluster.sh -k
  $ ./start_local_cluster.sh -s

How to submit virtual machines ?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First read :ref:`contextualization` section to understand how contextualization works with snooze.
Basically you have to generate an iso (*context.iso*) file and make it available from all the virtual machines.
This iso file contains the network settings used by your virtual machines.

Extra
^^^^^

* Configuring the snoozeweb will allow you to have a web gui for managing Snooze.
* Configuring properly rabbitmq will provide you real time infrastructure
  plotting in the snooze web interface (based on web sockets).
* By configuring Cassandra you will benefits from a reliable database
  to store a huge amount of monitoring datas and it will enable more features
  in the snoozeweb interface.

If you are interested in installing optionnal dependency of the Snooze Software (RabbitMQ, Cassandra...) have a look 
here :

:ref:`snoozeweb`

:ref:`rabbitmq`

:ref:`cassandra`
