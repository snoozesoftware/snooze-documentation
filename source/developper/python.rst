.. _python-driver:

Python driver
-----------------------

This driver make use of the internal API. Unless you have specific requirements,
we suggest to move to the EC2 API and the corresponding driver. 

Apache Libcloud ( http://libcloud.apache.org/) driver will allow you to interact with snooze from a python script.

You need the code of the apache-libcloud project :

::

    git clone https://github.com/apache/libcloud.git
    git checkout 0.12.1

Next, install the specific files for Snooze driver from : https://github.com/msimonin/snooze-libcloud

You need to add three files :

::

    libcloud/compute/providers.py
    libcloud/compute/drivers/snooze.py
    libcloud/compute/types.py


Use it
^^^^^^^

You can copy/paste the following code in the python interpreter or in a script file.

* First you need to import the requires libraries.

::

    from libcloud.compute.types import Provider
    from libcloud.compute.providers import get_driver

* Next, connect to the driver. Depending on your configuration pass one bootstrap address to the driver, here we assume that a bootstrap is listenning on *127.0.0.1:5000*.

::

    Snooze = get_driver(Provider.SNOOZE)
    driver = Snooze("127.0.0.1","5000")

Now you are ready to create a virtual machine and check its state.

::

    n1 = driver.create_node(libvirt_templates=["vmtemplate.xml"], tx=12800, rx=12800)
    print "VM %s status %s"%(n1.name,n1.state)


