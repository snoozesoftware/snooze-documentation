.. _dynamic-contextualization:

Dynamic Contextualization
--------------------------

Principles
^^^^^^^^^^

Dynamic contextualization mechanism works as the following :

* Test for the presence of a CD in the CD drive of the VM,
* if it exists, mount the CD, test the presence of a script post-install, and run it as root,
* if it does not exist, use dhcp on the first network interface. 

Contextualization
^^^^^^^^^^^^^^^^^

Copy the following script in your virtual machine under /usr/local/bin/contextualization.

::

      #!/bin/bash

      DEVICE=
      [ -b /dev/hdb ] && DEVICE=/dev/hdb
      [ -b /dev/sdb ] && DEVICE=/dev/sdb
      [ -b /dev/vdb ] && DEVICE=/dev/vdb
      [ -b /dev/xvdb ] && DEVICE=/dev/xvdb
      [ -b /dev/sr0 ] && DEVICE=/dev/sr0

      if [ -b "$DEVICE" ];then
      /bin/mount -t iso9660 $DEVICE /mnt 2> /dev/null

      if [ -f /mnt/post-install ]; then
      bash /mnt/post-install
      fi

      umount /mnt 2> /dev/null
      else
      ifup eth0
      fi

      exit 0

Open /etc/rc.local and call this script : 

::

    /usr/local/bin/contextualization


Your base image should be now ready!


Generate the iso context file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can download the context files from the Download page. Fill the files common/network and common/routes with your parameters. This parameters will be applied at boot time on the virtual machines.
Generate the context.iso file : 

::

    ($) genisoimage -RJ -o context.iso context

Generate the virtual machine template
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Assumed that the name of your backing file is debian-base.raw, you will need to execute the following command to create a COW image debian1.qcow2.

::

    ($) qemu-img create -b debian-base.raw -f qcow2  debian1.qcow2

You can now prepare the VM template. Currently, the client accepts native libvirt templates. It is important that you choose different names and UUIDs (by the way UUID can be omitted) for your VMs. Otherwise, subsequent submissions will fail due to naming conflicts. Moreover, disk image path and bridge name must be set correctly. An example libvirt VM template (debian1.xml) for the KVM hypervisor could look as follows:

::

      <domain type='kvm'>
      <name>debian1</name>
      <memory>524288</memory>
      <vcpu>1</vcpu>
      <os>
         <type arch="x86_64">hvm</type>
      </os>
      <clock sync="localtime"/>
      <on_poweroff>destroy</on_poweroff>
      <on_reboot>restart</on_reboot>
      <on_crash>destroy</on_crash>
      <devices>
         <emulator>/usr/bin/kvm</emulator>
         <disk type='file' device='disk'>
         <driver name='qemu' type='qcow2'/>
            <source file='/opt/cloud/debian1.qcow2'/>
            <target dev='vda' bus='virtio'/>
         </disk>
         <disk type='file' device='cdrom'>
           <source file='/opt/cloud/context.iso'/>
           <target dev='vdb' bus='virtio'/>
           <readonly/>
         </disk>
         <interface type='bridge'>
           <mac address='00:16:c0:A8:7a:11'/>
           <source bridge='virbr0'/>
         </interface>
         <serial type='pty'>
           <source path='/dev/ttyS0'/>
           <target port='0'/>
         </serial>
         <console type='pty'>
           <source path='/dev/ttyS0'/>
           <target port='0'/>
         </console>
      </devices>
      </domain>


Note that the bridge name virbr0 must correspond to the bridge configured on the LCs!
Note that the context.iso file must be accessible from your LCs.
