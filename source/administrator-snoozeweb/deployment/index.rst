.. _Downloads: http://snooze.inria.fr/download/

.. _snoozeweb:

Deployment of Snoozeweb
------------------------

The following sections deal with installing the Snoozeweb service.
It will give you a minimal web GUI to get started with Snooze.

* Get the code from the Downloads_ page and unpack it. 

* Be sure to have bundle installed. Otherwise you can : 

::

    gem install bundle

* Once bundle is installed, run 

::
    
    cd snoozeweb
    bundle install


* If the Snoozeweb service is on the same network as all the others system services of Snooze,
 , fill the *config.yml* with the right parameters and then you can simply run :

::

    ruby run.rb

* If the Snoozeweb service is not on the same network as all the others system services of Snooze, you will have to tunnel the connections (read *config.yml* to know what connections have to be tunneled.) 


Note that if you have deployed Snooze with the *snooze-capistrano* script, you can type : 

::

    BOOTSTRAP="you bootstrap" ruby g5k.rb

The previous command assumes that your login is the same on your local machine and on grid'5000 frontend. Otherwise open *g5k.rb* and change it.

