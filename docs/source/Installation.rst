Installation
============

In this manual you will learn to install honeypot (specifically, Dionaea and Cowrie honeypot) using existing docker images that have been created and distributed through github or docker hub.


Preparing The Honeypot Sensor
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cowrie
^^^^^^
Cowrie is a medium interaction honeypot that works on two protocols, Secure Shell (SSH) and Telnet. Cowrie will record any activity that happened in the honeypot throughout the whole session. 

Services and port number list:
Secure Shell (SSH) 	: port 22
Telnet 		        : port 23

In this section, you will learn how to install and run cowrie honeypot using docker. The installation of cowrie is implemented by using existing Dockerfile that is required to be reconfigured before the user builds the docker image. 

Firstly, clone the honeypot Dockerfile by using this command:

.. code-block:: RST

  Docker pull cowrie/cowrie

Then, run the docker image by using this command:

.. code-block:: RST

  Docker run cowrie:devel


At this moment, you would notice that cowrie honeypot us listening on port 2222 for SSH protocol and 2223 for telnet protocol. This however, will not ensure that you will gain any data since those two port numbers are considered as illegitimate ports usage. Thus, a change is needed to set the port number into the default port number for each service. In order to configure the settings of the docker image, you will need to access the docker image as root:

.. code-block:: RST

  docker exec -u 0 -ti container_id /bin/bash

Once you get inside the docker images as root, run this command to install the necessary tools in order to access the settings. 
 
.. code-block:: RST

  Apt-get update
  Apt-get install nano
  Apt-get install authbind

After that, access the “etc” directory inside the “cowrie-git” folder by using this command:

.. code-block:: RST

  Cd /cowrie-git/etc

Now, copy the the configuration file “cowrie.cfg.dist” into “cowrie.cfg” and open it using the command below:

.. code-block:: RST

  cp cowrie.cfg.dist cowrie.cfg
  nano cowrie.cfg

Then, change the configuration in the script:


.. code-block:: RST

  [ssh]
  enabled = true
  listen_endpoints = tcp:22:interface:0.0.0.0

  [telnet]
  enabled = true
  listen_endpoints = tcp:23:interface:0.0.0.0


  [output_hpfeeds]
  enabled = true
  server = ip_address
  port = 10000
  identifier = your_identifier
  secret = your secret
  debug = false

  [output_hpfeeds3]
  enabled = true
  server = ip_address
  port = 10000
  identifier = your_identifier
  secret = your_secret
  debug = false


Add the command above inside the script (“cowrie.cfg” file), save the changes and proceed to build the docker image by going to the path /home/your_user/tpotce/docker. When you reach that path, execute this command to build cowrie’s docker image:

.. code-block:: RST

  sudo docker build cowrie

Once it is finished, you will need to push the repository into docker hub so that you can run it. 
In order to push the docker image into docker hub, you should register yourself in the docker hub website. After that, initiate a docker login by using the command :

.. code-block:: RST

  Docker login --username=your_username --email=your_email@domain.com

If everything worked out, you will get a similar message

.. code-block:: RST

  WARNING: login credentials saved in /home/username/.docker/config.json
  Login Succeeded

Now, you can check your docker image. You will see similar messages 

.. Image

The repository “none” means that your docker image has been successfully built, but does not have any repository yet. Now, you have to push the docker image “none” to your repository. First of all, make sure to create a repository in your docker hub account through the website. Then, using this command to tag the docker image you would like to push into the repository:
docker tag image_id your_username/repo_name:tag_name

After you successfully tag your docker image, push it into the repository by using this command:

.. code-block:: RST

  docker push yourusername/repo_name

Once it is pushed, it will presentate the previously pushed docker image with the name of its repository



Finally, you just need to run the docker image that you have built. Simply enter the command below to run the docker:

.. code-block:: RST

  Sudo docker run image_name:tag_name

Note that if you can run the command without giving any input the tag of the docker image. However, the tag will be considered as latest by default. Therefore, it is recommended to use the complete command to avoid any confusion if you have docker images with the same name but different settings or configuration.

Listening on port 22 and 23
---------------------------

.. code-block:: RST

  Note: this service emulated by Cowrie so the attacker will be trapped

At this moment, you would notice that cowrie honeypot us listening on port 2222 for SSH protocol and 2223 for telnet protocol. This however, will not ensure that you will gain any data since those two port numbers are considered as illegitimate ports usage. Thus, a change is needed to set the port number into the default port number for each service. In order to configure the settings of the docker image, you will need to access the docker image as root:

.. code-block:: RST

  docker exec -u 0 -ti container_id /bin/bash

Once you get inside the docker images as root, run this command to install the necessary tools in order to access the settings. 
 

.. code-block:: RST

  Apt-get update
  Apt-get install nano
  Apt-get install authbind


After that, access the “etc” directory inside the “cowrie-git” folder by using this command:

.. code-block:: RST

  Cd /cowrie-git/etc

Now, copy the the configuration file “cowrie.cfg.dist” into “cowrie.cfg” and open it using the command below:

.. code-block:: RST

  Cp cowrie.cfg.dist cowrie.cfg
  Nano cowrie.cfg

  sudo touch /etc/authbind/byport/23
  sudo chown cowrie:cowrie /etc/authbind/byport/23
  sudo chmod 770 /etc/authbind/byport/23



Port redirection commands are system-wide and need to be executed as root. A firewall redirect can make your existing SSH server unreachable, remember to move the existing server to a different port number first.

The following firewall rule will forward incoming traffic on port 22 to port 2222 on Linux:

.. code-block:: RST

  sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222

Or for telnet:

.. code-block:: RST

  sudo iptables -t nat -A PREROUTING -p tcp --dport 23 -j REDIRECT --to-port 2223

.. code-block:: RST

  $ sudo touch /etc/authbind/byport/23
  $ sudo chown cowrie:cowrie /etc/authbind/byport/23
  $ sudo chmod 770 /etc/authbind/byport/23


Dionaea
^^^^^^^
Dionaea honeypot is a low interaction honeypot that works in multiple protocols that is listed below as well as its default port number:

.. code-block:: RST

  FTP		: port 20/TCP and 21/TCP
  Nameserver	: port 42/TCP
  TFTP		: port 69/UDP
  HTTP		: port 80/TCP 
  HTTPS		: port 443/TCP
  MSRPC		: port 135/TCP
  SNMP		: port 161/UDP
  SMB		: port 445/TCP
  MS-SQL	: port 1433/TCP
  MYSQL		: port 3306/TCP
  SIP		: port 5060/TCP
  SIP-TLS	: port 5061/TCP
  Memcached 	: port 11211 (both TCP and UDP)

In this section, you will learn how to install and run dionaea honeypot using docker. The installation of dionaea is implemented by using existing Dockerfile that is required to be reconfigured for personal use and enabling additional features. 

Firstly, clone the honeypot Dockerfile by using this command:

.. code-block:: RST

  Docker pull dinotools/dionaea-docker

Then, proceed to run the docker image by executing the command provided below:

.. code-block:: RST

  Docker run dinotools/dionaea-docker

After that, access the config folder inside the docker image that has been built as root/administrator account by entering the command : 

.. code-block:: RST

  Docker -u 0 -ti container_id /bin/bash

Once you proceed, enter the folder etc that is located with the specified path /opt/dionaea/etc/dionaea/ihandlers with the command 

.. code-block:: RST

  cd opt/dionaea/etc/dionaea/ihandlers

Once you change your directory there, you need to add “hpfeeds.yaml” inside the ihandlers folder. Inside the ihandler folder, execute this command to add the file “hpfeeds.yaml”. 

.. code-block:: RST

  Sudo nano hpfeeds.yaml

With the command above, it will display an empty file. You need to put these commands in order to implement the changes inside the docker image. 

.. code-block:: RST

  - name: hpfeeds
    config:
    # fqdn/ip and port of the hpfeeds broker
    server: "10.20.100.100"
    port: 10000
    ident: "sensor-dionaea"
    secret: "password1234"
  # dynip_resolve: enable to lookup the sensor ip through a webservice
    dynip_resolve: "http://hpfriends.honeycloud.net/ip"
  # Try to reconnect after N seconds if disconnected from hpfeeds broker
      # reconnect_timeout: 10.0

After that, you need to restart the docker container so that the changes that you have made before are implemented. This can be run through the command :

.. code-block:: RST

  Docker restart container_id


Finally, in order to ensure that the honeypot actually works, you can use net-tools to display which port have been utilized in order to ensure that the honeypot services have been successfully executed. It can be utilized by using this command:

.. code-block:: RST

  Netstat -plnt

After that, make sure every protocol that you enabled in dionaea (all services are enabled by default settings) is listening to the proper port (default port number of each service). You can check it from the screenshot below. To ensure that all of your services provided by dionaea are running on default ports, please refer to the brief explanation of cowrie in the section above.

.. image


Preparing HPFeeds
^^^^^^^^^^^^^^^^^
The followings are the built for HPFeeds (mostly from scratch)

HPFeeds MongoDB Scratch Built 1 Container
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following are the built for HPFeeds from scsratch for 1 container:

1.	First, update your existing list of packages

.. code-block:: RST

  $ sudo apt update

2.	Next install a few prerequisite package

.. code-block:: RST

  $ sudo apt install docker.io

3.	Run mongo docker 

.. code-block:: RST

  $ docker run -d -p 27017-27019:27017-27019 --name mongodb mongo:latest

4.	We can do anything what we want with the docker but first of all it’s always better to update and upgrade the docker first:

.. code-block:: RST

  $ apt-get update && apt-get upgrade -y

5.	After we finish updating and upgrading, we need to install wget git nano sudo:

.. code-block:: RST

  $   apt install -y ubuntu-server wget git nano sudo

6.	Because we install the ubuntu-server there will be some configuration that we should config but for the simplicity sake I have listed below my answer:

.. code-block:: RST

  $   31

.. image:: images/hpfeeds-choosing-country.png
   :width: 600

.. code-block:: RST

  $ 1

.. image:: images/hpfeeds-choose-match-keyboard.png
   :width: 600

.. code-block:: RST

  $ 1

.. image:: images/hpfeeds-configuration-console-setup.png
   :width: 800

.. code-block:: RST

  $ 20

.. image:: images/hpfeeds-choosing-character-set.png
   :width: 800

7.	We need to clone hpfeeds by typing the command above:

.. code-block:: RST

  $ git clone https://github.com/pwnlandia/mhn.git

8.	Go to mhn/scripts by typing the command above:

.. code-block:: RST

  $ cd mhn/scripts

9.	We need to install hpfeeds by execute this command:

.. code-block:: RST

  $ ./install_hpfeeds.sh

10.	After the installation of hpfeeds we need to install mnemosyne we can do that by executing this:

.. code-block:: RST

  $ ./install_mnemosyne.sh

11.	To check the successful installation and to check the process we can type the command below for checking the hpfeeds process:

.. code-block:: RST

  $ supervisorctl status hpfeeds-broker

and the result can be similar to this:

.. image:: images/hpfeeds-checking-hpfeeds-process.png
   :width: 800

Congrats! You have installed the HPFeeds from Scratch in 1 container!

HPFeeds MongoDB Scratch Built Separate Container
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The following are the built for HPFeeds MongoDB from scratch for separate container:

1.	First, update your existing list of packages

.. code-block:: RST

  $ sudo apt update

2.	Next install a few prerequisite package

.. code-block:: RST

  $ sudo apt install docker.io

3.	Run mongo docker 

.. code-block:: RST

  $ docker run -d -p 27017-27019:27017-27019 --name mongodb mongo:latest

4.	After We run docker mongo we need to run another docker ubuntu for hosting docker HPFeeds we can do that by typing:

.. code-block:: RST

  $ docker run -ti --network=host --name hpfeeds1804 ubuntu:bionic

5.	We can do anything what we want with the docker but first of all it’s always better to update and upgrade the docker first:

.. code-block:: RST

  $ apt-get update && apt-get upgrade -y

6.	After we finish updating and upgrading, we need to install wget git nano sudo:

.. code-block:: RST

  $   apt install -y ubuntu-server wget git nano sudo

7.	Because we install the ubuntu-server there will be some configuration that we should config but for the simplicity sake I have listed below my answer:

.. code-block:: RST

  $   31

.. image:: images/hpfeeds-choosing-country.png
   :width: 800

.. code-block:: RST

  $ 1

.. image:: images/hpfeeds-choose-match-keyboard.png
   :width: 600

.. code-block:: RST

  $ 1

.. image:: images/hpfeeds-configuration-console-setup.png
   :width: 800

.. code-block:: RST

  $ 20

.. image:: images/hpfeeds-choosing-character-set.png
   :width: 800

8.	We need to clone hpfeeds by typing the command above:

$ git clone https://github.com/pwnlandia/mhn.git

9.	Go to mhn/scripts by typing the command above:

$ cd mhn/scripts

10.	We need to install hpfeeds by execute this command:

$ ./install_hpfeeds.sh

11.	After the installation of hpfeeds we need to install mnemosyne we can do that by executing this:

$ ./install_mnemosyne.sh

12.	To check the successful installation and to check the process we can type the command below for checking the hpfeeds process:

$ supervisorctl status hpfeeds-broker

and the result can be similar to this:

.. image:: images/hpfeeds-checking-hpfeeds-process.png
   :width: 800

Congrats!!! You have built the HPFeeds from Scratch in separate container!
