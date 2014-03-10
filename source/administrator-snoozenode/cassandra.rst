
.. _cassandra:

Cassandra
------------

Since version 2.0 you can use the Cassandra distributed database as an alternative 
to the in memory database.

1. Installation 
^^^^^^^^^^^^^^^^
Install cassandra from the package manager of your distribution.

*on debian, install cassandra*

Alternatively you can use a proper puppet module.

2. Upload the schema
^^^^^^^^^^^^^^^^^^^^

You will find the schema of the database in the Downloads_ page.
Load it into cassandra : 

::

  cassandra-cli -f schemaup.cas

This will create all the column families needed by Snooze.

2. Setup 
^^^^^^^^^

The snooze node config file (*snooze_node.cfg*) contains a section dealing with  the configuration of the cassandra

::

  #coma separated list of cassandra hosts
  database.cassandra.hosts = <address:port>

  # Maximum number of monitoring entries to keep per group manager
  database.numberOfEntriesPerGroupManager = 20

  # Maximum number of monitoring entries to keep per virtual machine
  database.numberOfEntriesPerVirtualMachine = 30

Note that when using Cassandra *database.numberOfEntriesPerGroupManager* and *database.numberOfEntriesPerVirtualMachine* correspond to the number of seconds the  monitoring values will be stored. It uses the TTL feature of the cassandra columns.
