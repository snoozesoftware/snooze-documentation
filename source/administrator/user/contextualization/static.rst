.. _static-contextualization:

Static Contextualization
--------------------------

Create the base image
^^^^^^^^^^^^^^^^^^^^^^^

Get the init script from our Download page. It was tested on the Debian as well as Ubuntu operating system. Please move it to your /etc/init.d/ directory and run the following command to update the init script links.

::

    ($) update-rc.d vmcontext defaults

Your base image should be now ready!


Generate the template
^^^^^^^^^^^^^^^^^^^^^^

Assumed that the name of your backing file is debian-base.raw, you will need to execute the following command to create a COW image debian1.qcow2

::

    ($) qemu-img create -b debian-base.raw -f qcow2  debian1.qcow2


You can now prepare the VM template. Currently, the client accepts native libvirt templates. It is important that you choose different names and UUIDs (by the way UUID can be omitted) for your VMs . Otherwise, subsequent submissions will fail due to naming conflicts. Moreover, disk image path and bridge name must be set correctly. An example libvirt VM template (debian1.xml) for the KVM hypervisor could look as follows:

::

  <domain type='kvm'>
    <name>debian1</name>
    <uuid>0f476e56-67ea-11e1-858e-00216a972a36</uuid>
    <memory>597152</memory>
    <currentMemory>597152</currentMemory>
    <vcpu>1</vcpu>
    <os>
      <type arch='x86_64' machine='pc-0.12'>hvm</type>
      <boot dev='hd'/>
    </os>
    <features>
      <acpi/>
      <apic/>
      <pae/>
    </features>
    <clock offset='utc'/>
    <on_poweroff>destroy</on_poweroff>
    <on_reboot>restart</on_reboot>
    <on_crash>restart</on_crash>
    <devices>
      <emulator>/usr/bin/kvm</emulator>
      <disk type='file' device='disk'>
        <driver name='qemu' type='qcow2'/>
        <source file='/opt/cloud/debian1.qcow2'/>
        <target dev='vda' bus='virtio'/>
        <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0'/>
      </disk>
      <controller type='ide' index='0'>
        <address type='pci' domain='0x0000' bus='0x00' slot='0x01' function='0x1'/>
      </controller>
      <interface type='bridge'>
        <mac address='52:54:00:83:25:2b'/>
        <source bridge='virbr0'/>
      </interface>
      <serial type='pty'>
        <target port='0'/>
      </serial>
      <console type='pty'>
        <target type='serial' port='0'/>
      </console>
      <graphics type='vnc' port='-1' autoport='yes' listen='0.0.0.0'/>
      <input type='tablet' bus='usb'/>
      <input type='mouse' bus='ps2'/>
      <memballoon model='virtio'>
        <address type='pci' domain='0x0000' bus='0x00' slot='0x06' function='0x0'/>
      </memballoon>
    </devices>
  </domain>

Note that the bridge name virbr0 must correspond to the bridge configured on the LCs!





