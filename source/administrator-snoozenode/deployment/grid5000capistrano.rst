.. _Downloads: http://snooze.inria.fr/download/

Multi-Nodes Cluster on Grid'5000 using capistrano.
--------------------------------------------------

This page deals with the deployment of the Snoozenode, Snoozeimages and SnoozeEC2 system services. 

Get the deployment script
^^^^^^^^^^^^^^^^^^^^^^^^^

The prefer way of getting the code is to clone the repository.

::

  git clone git://github.com/msimonin/snooze-capistrano.git

Move to the release ( tag ) of you choice with : 

::

  cd snooze-capistrano
  git checkout vx.x.x


Alternatively you can get it from the Downloads_ page and install it.

Have a correct ruby environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This deployment assumes that ruby >= 1.9.3 and require bundler gem.

Install bundler

:: 

  gem install bundler

Check rvm if you need to deal with several ruby environments.

Once it's done, you just have to launch in the *snooze-capistrano* directory:

::

    bundle install

Configure Restfully
^^^^^^^^^^^^^^^^^^^

The script makes use of Restfully to manage connections to the Grid'5000 API.

Create the file *~/.restfully/api.grid5000.fr.yml* with this content : 

::

  uri: https://api.grid5000.fr/stable/grid5000
  username: login
  password: xxxxx


Check the deployment parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Open *config/deploy.rb* and replace the ssh settings with you own settings.

The deployment script will generates an iso file on your local machine.
You'll need to tell to the script what tool need to be used to generate this iso.

* In *config/deploy.rb* set the *mkisotool* to the right command.

Note : yes, I know it is not very practical, and should be changed in the next version of the script.

* Open *config/deploy/xp5k/xp5k_[version]* to check the deployment parameters (number of nodes ...)


Automatic deployment
^^^^^^^^^^^^^^^^^^^^

You're now ready to launch the deployment : 

::

  cap  automatic

And to start snooze cluster with : 

::

  cap snooze:cluster:start


Web Interface
^^^^^^^^^^^^^

Please refer to :ref:`snoozeweb` to install the web interface.

Overview of the tasks available
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* cap -T will give you the available tasks for your deployment.

::

  cap automatic               # Automatic deployment
  cap cassandra               # Deploy Cassandra on nodes
  cap cassandra:opscenter     # Install the opscenter
  cap cassandra:schema        # Install the database schema
  cap clean                   # Remove all running jobs
  cap deploy                  # Deploy with Kadeploy
  cap describe                # Describe the cluster
  cap experimental            # Set the target stage to `experimental'.
  cap invoke                  # Invoke a single command on the remote servers.
  cap master                  # Set the target stage to `master'.
  cap multistage:prepare      # Stub out the staging config files.
  cap nfs                     # Deploy NFS on nodes
  cap nfs:client              # Configure NFS clients
  cap nfs:server              # Configure NFS server
  cap rabbitmq                # Deploy RabbitMQ on nodes
  cap sandbox                 # Set the target stage to `sandbox'.
  cap shell                   # Begin an interactive Capistrano session.
  cap snooze                  # Install Snooze on nodes
  cap snooze:cluster          # Configure the cluster
  cap snooze:cluster:shutdown # Power down localcontrollers
  cap snooze:cluster:start    # Start cluster
  cap snooze:cluster:stop     # Stop cluster
  cap snooze:cluster:wakeup   # Wake Up localcontrollers
  cap snooze:plugins          # Install plugins
  cap snooze:prepare          # prepare the nodes
  cap snooze:provision        # Provision all the nodes
  cap submit                  # Submit jobs
  cap v2.1.0                  # Set the target stage to `v2.1.0'.


Basic usage
^^^^^^^^^^^

If you want to change a snoozenode parameter:

* Check the file *recipes/snooze/recipe.rb*

* Make your change

* stop, provision and restart your cluster : 

::

  cap snooze:cluster:stop snooze:provision snooze:cluster:start





