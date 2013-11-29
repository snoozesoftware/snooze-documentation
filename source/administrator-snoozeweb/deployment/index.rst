====================
Deployment
====================

The following sections deal with installing the Snoozeweb service.
It will give you a minimal web GUI to get started with Snooze.

* Clone the source repository : 

::

    git clone https://github.com/msimonin/snoozeweb.git

* Be sure to have bundle installed. Otherwise you can : 

::

    gem install bundle

* On bundle is installed, run 

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

    bootstrap="you bootstrap" ruby g5k.rb
