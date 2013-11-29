.. _Downloads: http://snooze.inria.fr/download/
.. _GitDeployLocalCluster: http://github.com/snoozesoftware/snooze-deploy-localcluster/

Cluster
--------------------

This page describes how to deploy Snooze on a traditional cluster with one management node hosting all the core system services (i.e. *NFS, Apache ZooKeeper, Bootstrap node, Group leader, and one Group manager*) and the rest of the nodes serving as compute resources (i.e. *local controllers*).

Note that our preferred Linux distribution is Debian. Consequently, the documentation below requires a working Debian installation. However, with some efforts you should be able to install Snooze on any Linux distribution by using the provided Binary (see Downloads_ section).

If you have managed to install Snooze on your favorite distribution please do not hesitate to contact us with your documentation. We will review and publish it.

Management Node
****************

Management node require : 

* *Multicast-routing enabled switch*
* *Hardware supporting the KVM/XEN hypervisor*
* *Debian Squeeze (Sid pinned) or wheezy operating system*


Prepare the environment
^^^^^^^^^^^^^^^^^^^^^^^

::

            $ apt-get install nfs-server ipmitool sudo sun-java6-jre sun-java6-bin zookeeper zookeeperd
            $ mkdir -p /opt/cloud
            $ echo "/opt/cloud *(rw,sync,no_root_squash,no_subtree_check)" >> /etc/exports
            $ /etc/init.d/nfs-kernel-server restart
            $ groupadd snooze
            $ useradd -d /opt/cloud -s /bin/false snoozeadmin -g snooze
            $ "visudo" and add: "snoozeadmin ALL = NOPASSWD: /usr/bin/ipmitool" to the user privilege section

Install and configure Snooze
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Download and install the latest snoozenode package. You can find the latest snoozenode package on the Downloads_ page.

::
            $ dpkg -i snoozenode_X.X-X_all.deb

Replicate Snooze and logger configuration files for BN, GL, and GM:

::

            $ cp /usr/share/snoozenode/configs/snooze_node.cfg /usr/share/snoozenode/configs/snooze_node_bn.cfg
            $ cp /usr/share/snoozenode/configs/snooze_node.cfg /usr/share/snoozenode/configs/snooze_node_gm_1.cfg
            $ cp /usr/share/snoozenode/configs/snooze_node.cfg /usr/share/snoozenode/configs/snooze_node_gm_2.cfg

            $ cp /usr/share/snoozenode/configs/log4j.xml /usr/share/snoozenode/configs/log4j_bn.xml
            $ cp /usr/share/snoozenode/configs/log4j.xml /usr/share/snoozenode/configs/log4j_gm_1.xml
            $ cp /usr/share/snoozenode/configs/log4j.xml /usr/share/snoozenode/configs/log4j_gm_2.xml

            $ cp /etc/init.d/snoozenode /etc/init.d/snoozenode_bn
            $ cp /etc/init.d/snoozenode /etc/init.d/snoozenode_gm_1
            $ cp /etc/init.d/snoozenode /etc/init.d/snoozenode_gm_2

Modify the snoozenode configuration files: snooze_node_bn.cfg, snoozenode_gm_1.cfg,snoozenode_gm_2.cfg.
In the bootstrap node configuration file set:

::

                        node.role = bootstrap

In the first group manager configuration file set:

::

                        node.role = groupmanager
                        network.listen.controlDataPort = 5001
                        network.listen.monitoringDataPort = 6001
                        network.virtualMachineSubnet = XXX.XXX.XXX.0/24 (your VM subnet)

In the second group manager configuration file set:

::

                        node.role = groupmanager
                        network.listen.controlDataPort = 5002
                        network.listen.monitoringDataPort = 6002
                        network.virtualMachineSubnet = XXX.XXX.XXX.0/24 (your VM subnet)

Modify the logger configuration files: log4j_bn.xml, log4j_gm_1.xml, and log4j_gm_2.xml

change the output file name “snooze_node.log” to “snooze_node_bn.log”, “snoozenode_gm_1.log”, and “snoozenode_gm_2.log” respectively

Modify the snoozenode init scripts: snoozenode_bn, snoozenode_gm_1, and snoozenode_gm_2
Change the following lines in the same manner as the logger configuration files:

::

    # Provides: snoozenode -> Change to snoozenode_bn/gm_1/gm_2
    SCRIPT_NAME="snoozenode" -> Change to snoozenode_bn/gm_1/gm_2
    PID_FILE="/var/run/snoozenode.pid" ->  Change to snoozenode_bn/gm_1/gm_2.pid
    SYSTEM_CONFIGURATION_FILE="$CONFIGS_DIRECTORY/snooze_node.cfg" -> Change to snooze_node_bn/gm_1/gm_2.cfg
    LOGGER_CONFIGURATION_FILE="$CONFIGS_DIRECTORY/log4j.xml" -> Change to log4j_bn/gm_1/gm_2.xml

Update the init script links:

::

    $ update-rc.d -f snoozenode remove
    $ update-rc.d snoozenode_bn defaults
    $ update-rc.d snoozenode_gm_1 defaults
    $ update-rc.d snoozenode_gm_2 defaults

    $ /etc/init.d/snoozenode_bn start
    $ /etc/init.d/snoozenode_gm_1 start
    $ /etc/init.d/snoozenode_gm_2 start

You now should have one bootstrap node, group leader, and group manager running. Please refer to logs (/tmp/snooze_node_*.log) to see if all components are running. For example bootstrap node should be receiving group leader heartbeats.


On all compute nodes
**********************

Prepare the environment
^^^^^^^^^^^^^^^^^^^^^^^

Install the dependencies

::

    $ apt-get install kvm qemu-kvm libvirt-bin libvirt-dev bridge-utils nfs-common sudo sun-java6-jre sun-java6-bin
    $ apt-get install ipmitool pm-utils

Modify the /etc/hosts file

::

    127.0.0.1  localhost.localdomain localhost
    XXX.XXX.XXX.XXX  yourHostName

Make sure your set the right IP and hostname of your main network interface.
Enable bride networking /etc/network/interfaces

If your main network interface is *eth0* modify as follows:

::

    auto lo
    iface lo inet loopback

    auto br0
    iface br0 inet dhcp
    bridge_ports eth0
    bridge_stp on
    bridge_maxwait 0

Restart networking:

::

    $ /etc/init.d/networking restart

Please see http://wiki.debian.org/BridgeNetworkConnections for more details on bridge network connections.
Ensure NFS is remounted in case the node gets rebooted: modify /etc/rc.local and add

::

    mount -a

Add snooze group and snoozeadmin user to the system

::

    $ groupadd snooze
    $ useradd -d /opt/cloud -s /bin/false snoozeadmin -g snooze -G kvm

Configure the libvirt daemon by editing the following files libvirtd.conf, libvirt-bin, and qemu.conf

In /etc/libvirt/libvirtd.conf: disable TLS and enable TCP listing, disable TCP authentication:

::

    listen_tls = 0
    listen_tcp = 1
    auth_tcp = "none"

In /etc/default/libvirt-bin

::

    libvirtd_opts="-d -l"

In /etc/libvirt/qemu.conf enter the snooze user and group information:

::

    user = "snoozeadmin"
    group = "snooze"

Restart the libvirt daemon:

::

    $ /etc/init.d/libvirt-bin restart

Configure the NFS server

::

    $ mkdir /opt/cloud
    $ echo "<MANAGEMENT_NODE_IP>:/opt/cloud /opt/cloud nfs rw 0 0" >> /etc/fstab
    $ mount -a

If you need power management features “visudo” and add to the user privilege section:

::

   snoozeadmin ALL = NOPASSWD: /sbin/shutdown
   snoozeadmin ALL = NOPASSWD: /usr/bin/ipmitool
   snoozeadmin ALL = NOPASSWD: /usr/sbin/pm-suspend
   snoozeadmin ALL = NOPASSWD: /usr/sbin/pm-hibernate
   snoozeadmin ALL = NOPASSWD: /usr/sbin/pm-suspend-hybrid
   snoozeadmin ALL = NOPASSWD: /usr/sbin/s2ram
   snoozeadmin ALL = NOPASSWD: /usr/sbin/s2disk
   snoozeadmin ALL = NOPASSWD: /usr/sbin/s2both

Install and configure Snooze
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Download and install the latest snoozenode package.You can find the latest snoozenode package on the Downloads_ page.

::

    $ dpkg -i snoozenode_X.X-X_all.deb

Configure the snoozenode daemon. Open the configuration file: /usr/share/snoozenode/configs/snooze_node.cfg

Set the node.role

::

    node.role = localcontroller

If you require power management, specify how this LC should be woken up. In this example the node is woken up using IPMI. Note that depending on your hardware different options must be passed to the IPMI driver. Currently, IPMI driver internally calls the ipmitool to enforce the commands. Note that you must verify that IPMI wake up works prior entering any information here, otherwise wake ups will fail.

::

    # Wakeup driver (IPMI, WOL, kapower3, test)
    energyManagement.drivers.wakeup = IPMI

    # Wakeup driver specific options
    # For IPMI
    energyManagement.drivers.wakeup.options = -I lanplus -H <BMC_IP> -U <user> -P <password>

Start the snoozenode daemon

::

    $ /etc/init.d/snoozenode start


Your Snooze cluster should be now up and running. Please see the log file (/tmp/snooze_node.log) to verify that everything works as it should and install more LCs if needed. You can also use the CLI to visualize the current system state. Please refer to the User manual for more information.

Note that Snooze deployment is not limited to the scenario described above. For example you could imagine installing all the system components (Apache ZooKeeper, BN, and more GMs) on dedicated servers to enhance scalability and fault tolerance. On the other hand you could also have a configuration in which servers act as management as well as compute nodes (i.e. having GM and LC on the same host). You should be able to realize such configurations with little effort after following this tutorial. 
