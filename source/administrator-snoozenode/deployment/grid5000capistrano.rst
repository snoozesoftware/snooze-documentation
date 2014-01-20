.. _Downloads: http://snooze.inria.fr/download/

Multi-Nodes Cluster on Grid'5000 using capistrano.
--------------------------------------------------

This page deals with the deployment of the Snoozenode, Snoozeimages and SnoozeEC2 system services. 

Get the deployment script
^^^^^^^^^^^^^^^^^^^^^^^^^

The prefer way of getting the code is to clone the repository.

::

  git clone git://github.com/msimonin/snooze-capistrano.git

Move to the release ( tag ) of your choice with : 

::

  cd snooze-capistrano
  git checkout v2.1.1


Alternatively you can get it from the Downloads_ page and install it.

Note that not checking out a particular tag will give you the latest version (not released yet and thus under development).


Have a correct ruby environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This deployment assumes that ruby >= 1.9.3 and requires bundler gem.

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

  base_uri: https://api.grid5000.fr/stable/grid5000
  username: "login"
  password: "xxxxx"


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


Note : I'll have to remove the previously created *.xp_cache* before resubmitting new jobs.


Web Interface
^^^^^^^^^^^^^

The web interface is installed on the bootstrap node at the end of the deployment. 
Assuming that the bootsrap node is *paradent-4*, open you browser and connect to the following url : 

::

  https://paradent-4.rennes.proxy-http.grid5000.fr


AMQP messages can be captured by the browser to display and update the system hierarchy. 
You just have to create a ssh tunnel from your local machine to the bootstrap node (hosting the rabbitmq broker).

::

  ssh -NL 55674:bootstrap:55674 login@access.grid5000.fr


Alternatively, if something went wrong during the deployment of the web interface. You can follow :ref:`snoozeweb`
to install this interface on your local machine.

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

==========================  ========================================
I would like to change ...  file
==========================  ========================================
My ssh keys                 config/deploy.rb
The site                    config/deploy.rb
The walltime                config/deploy.rb
The number of nodes         config/xp5k/xp5k_2.x.x.rb
The snooze parameters       recipes/snooze/templates/snoozenode.erb
==========================  ========================================

After changing the snooze parameters you can invoke :

::

  cap snooze:cluster:stop snooze:provision snooze:cluster:start

It will reprovision the cluster and restart it with the new parameters.

If you need to change the topology (eg : number of groupmanagers), you will have to redeploy the whole clutser.

Customize deployment
^^^^^^^^^^^^^^^^^^^^

If you would like to add steps in the deployment (typically at the end of the original deployment) 
a good practice is to add a new stage in the capistrano deployment process.

* Create a new stage by copying an existing one : 

::

  cp config/deploy/v2.1.0.rb config/deploy/mystage.rb

and add it to capistrano in the *config/deploy.rb* in the line :

:: 

  set :stages, %w(... mystage ...)
  set :default_stage, "mystage" # optional

Now *cap mystage command* or *cap v2.1.0 command* since you have copied *v2.1.0.rb*.

Setting mystage as default stage will allow you to invoke *command* without specifying which state you want : *cap command*

* Add your recipe in *mystage.rb* :

::

  recipes = [..., ..., ..., myrecipe]
  [...]
  after ..., ..., ..., myrecipe

You just have to create your recipe under *recipes/recipe/myrecipe/recipe.rb* and it will be invoked at the end of the deployment.


Directory Structure
^^^^^^^^^^^^^^^^^^^

::

  Capfile
  ├── config
  │   ├── deploy
  │   │   ├── experimental.rb
  │   │   ├── latest.rb
  │   │   ├── master.rb
  │   │   ├── sandbox.rb
  │   │   └── xp5k
  │   │       ├── xp5k_2.x.rb
  │   │       ├── xp5k_common_roles.rb
  │   │       ├── xp5k_common_tasks.rb
  │   │       ├── xp5k_master.rb
  │   │       ├── xp5k_sandbox.rb
  │   │       └── xp5k_testing.rb
  │   ├── deploy.rb
  │   └── lib
  │       └── spinner.rb
  ├── Gemfile
  ├── Gemfile.lock
  ├── README.md
  ├── recipes
  │   ├── cassandra
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── schema
  │   │   │   ├── schemadown.cas
  │   │   │   └── schemaup.cas
  │   │   ├── templates
  │   │   │   └── cassandra.erb
  │   │   └── tmp
  │   │       ├── cassandra.pp
  │   │       └── readme.txt
  │   ├── dfs
  │   │   ├── module
  │   │   │   └── manifests
  │   │   │       └── init.pp
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   ├── dfs-client.erb
  │   │   │   └── glusterfs.erb
  │   │   └── tmp
  │   │       └── readme.txt
  │   ├── keystone
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   ├── keystone.erb
  │   │   │   └── keystone.erb~
  │   │   └── tmp
  │   │       ├── keystone.pp
  │   │       ├── rabbitmq.pp
  │   │       └── readme.txt
  │   ├── nfs
  │   │   ├── module
  │   │   │   ├── files
  │   │   │   │   └── etc
  │   │   │   │       └── idmapd.conf
  │   │   │   └── manifests
  │   │   │       ├── init.pp
  │   │   │       └── server.pp
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   ├── nfs-client.erb
  │   │   │   ├── nfs_server.erb
  │   │   │   └── nfs-server.erb
  │   │   └── tmp
  │   │       ├── nfs-client.pp
  │   │       ├── nfs-server.pp
  │   │       └── readme.txt
  │   ├── rabbitmq
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   └── rabbitmq.erb
  │   │   └── tmp
  │   │       ├── rabbitmq.pp
  │   │       └── readme.txt
  │   ├── snooze
  │   │   ├── network
  │   │   │   ├── configure_network.sh
  │   │   │   ├── context
  │   │   │   │   ├── common
  │   │   │   │   │   ├── network
  │   │   │   │   │   └── routes
  │   │   │   │   ├── context.sh
  │   │   │   │   ├── distributions
  │   │   │   │   │   └── debian
  │   │   │   │   │       └── 00_network
  │   │   │   │   ├── init.sh
  │   │   │   │   ├── lib
  │   │   │   │   │   ├── functions
  │   │   │   │   │   └── mac-ip.cfg
  │   │   │   │   └── post-install
  │   │   │   └── interfaces
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   ├── network.erb
  │   │   │   └── snoozenode.erb
  │   │   └── tmp
  │   │       ├── bootstrap.pp
  │   │       ├── context.iso
  │   │       ├── groupmanager.pp
  │   │       ├── localcontroller.pp
  │   │       └── readme.txt
  │   ├── snoozeec2
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   └── snoozeec2.erb
  │   │   └── tmp
  │   │       ├── readme.txt
  │   │       └── snoozeec2.pp
  │   ├── snoozeimages
  │   │   ├── output.rb
  │   │   ├── recipe.rb
  │   │   ├── roles.rb
  │   │   ├── templates
  │   │   │   └── snoozeimages.erb
  │   │   └── tmp
  │   │       ├── readme.txt
  │   │       └── snoozeec2.pp
  │   └── snooze_webinterface
  │       ├── output.rb
  │       ├── recipe.rb
  │       ├── roles.rb
  │       └── tmp
  │           └── readme.txt
  ├── xp.conf
  └── xp.log





    









