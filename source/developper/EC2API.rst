EC2 API
--------


Supported Actions
^^^^^^^^^^^^^^^^^^

* ``DescribeInstances``

* ``DescribeImages``

* ``RunInstances``

* ``TerminateInstances``

* ``RebootInstances``


Use with libcloud driver
^^^^^^^^^^^^^^^^^^^^^^^^

::

  from libcloud.compute.types import Provider
  from libcloud.compute.providers import get_driver
  import time

  EC2_ACCESS_KEY = ""
  EC2_SECRET_KEY = ""

  Driver = get_driver(Provider.EUCALYPTUS)
  conn = Driver(
  EC2_ACCESS_KEY, 
  secret=EC2_SECRET_KEY,
  host="localhost",
  secure=False,
  port=4001,
  path="/")

  images = conn.list_images()
  print images
  sizes = conn.list_sizes()
  print sizes
  node = conn.create_node(image=images[0], size=sizes[1], ex_mincount = 1, name = "vm")
  time.sleep(5)
  nodes = conn.list_nodes()
  print nodes

