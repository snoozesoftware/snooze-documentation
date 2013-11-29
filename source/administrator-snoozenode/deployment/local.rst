.. _Downloads: http://snooze.inria.fr/download/
.. _GitDeployLocalCluster: http://github.com/snoozesoftware/snooze-deploy-localcluster/

Single Machine Setup
--------------------

Local deployment involves running all the Snooze system components: Bootstrap Node (BN), Group Leader (GL), Group Managers (GMs), and Local Controller (LCs) as well as dependencies: Apache ZooKeeper and libvirtd on the same machine (e.g. your laptop). 


Hardware and OS requirement
^^^^^^^^^^^^^^^^^^^^^^^^^^^
* Networking connection for multicast
* Hardware supporting the KVM hypervisor 
* Debian wheezy or Ubuntu (12.04 or newer)

Prepare the environment
^^^^^^^^^^^^^^^^^^^^^^^

Install the Java runtime environment, Apache ZooKeeper, and QEMU/KVM.

:: 

  $ apt-get install openjdk-6-jre zookeeper zookeeperd qemu-kvm libvirt-bin libvirt-dev
  $ groupadd snooze
  $ useradd -s /bin/false snoozeadmin -g snooze

Add User *snoozeadmin* the libvirt group.

::

  (Debian)$ adduser snoozeadmin libvirt
  (Ubuntu)$ adduser snoozeadmin libvirt

Install Snooze
^^^^^^^^^^^^^^

Get the latest Snooze client and node Debian packages from the Downloads_ page and install them.

::

  dpkg -i snoozeclient_X.X-X_all.deb
  dpkg -i snoozenode_X.X-X_all.deb



Use the local deployment script
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The local deployment script is made to facilitative the start of a local cluster. Particularly, it automatically creates all the configuration files needed to run multiple Snooze and libvirt daemons on a single machine. Moreover it launches the daemons and the required dependencies (e.g. the Apache ZooKeeper service).

You can get the local deployment script from the Downloads_ page (or from the `Github repository <http://github.com/snoozesoftware/snooze-deploy-localcluster/>`_). Unpack the archive and configure your Snooze deployment scenario. In other words, you need to inform the script how many BNs, GMs, and LCs you would like to run on your host. Therefore open the ./scripts/settings.sh file in localcluster subdirectory and look for the following lines.


::

  number_of_bootstrap_nodes=1
  number_of_group_managers=3
  number_of_local_controllers=2

Adopt them to your needs and you are almost done. You are now ready to instruct the script to launch the libvirtd and Snooze daemons. Note that in case you already have a libvirt and snoozenode daemon running (default installation), they needs to be stopped to avoid possible port collisions.


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

