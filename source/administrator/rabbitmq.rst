RabbitMQ
------------

Since version 2.0 event messages can be sent on an RabbitMQ queue.

1. Installation 
^^^^^^^^^^^^^^^^
Install rabbitmq server from the package manager of your distribution.

*on debian, install rabbitmq-server*


2. Setup 
^^^^^^^^^

The snooze node config file (*snooze_node.cfg*) contains a section dealing with  the configuration of the rabbitmq

::

  external.notifier.transport = RABBITMQ
  external.notifier.address = localhost
  external.notifier.port = 5672
  external.notifier.username = snooze
  external.notifier.password = snooze
  external.notifier.vhost = snooze-vhost
  external.notifier.faultTolerance.numberOfRetries = 10
  external.notifier.faultTolerance.retryInterval = 10

Setup user and vhost accordingly to the *snooze_node.cfg* parameters :

::

  rabbitmqctl add_user snooze snooze
  rabbitmqctl add_vhost snooze-vhost
  rabbitmqctl set_permissions -p snooze-vhost snooze ".*" ".*" ".*"


see :ref:`rabbitmq-message-format`
