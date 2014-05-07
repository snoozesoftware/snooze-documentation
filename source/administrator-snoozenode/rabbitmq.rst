.. _rplugins: http://www.rabbitmq.com/web-stomp.html

.. _rabbitmq:

RabbitMQ
------------

Since version 2.0 event messages can be sent on an RabbitMQ queue.

1. Installation 
^^^^^^^^^^^^^^^^
Install rabbitmq server from the package manager of your distribution.

*on debian, install rabbitmq-server*

Alternatively, you can use a the ``puppetlabs/rabbitmq`` puppet module to install rabbitmq.


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

For further documentation about the message format used : see :ref:`rabbitmq-message-format`

3. Enable extra plugins
^^^^^^^^^^^^^^^^^^^^^^^^

Snooze web makes use of the stomp protocol to plot the hierarchy real time.
You will need to install the *web stomp plugin* in order to activate this feature
See the rplugins_ page for instructions.
