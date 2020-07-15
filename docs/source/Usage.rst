Usage
=====

In this manual you will learn to use the honeypot (specifically, HPFeeds and MISP with the community) using existing docker images that have been created and distributed through github or docker hub.

Starting HPFeeds
^^^^^^^^^^^^^^^^
The following are the steps of how to start to use HPFeeds.

Start Using HPFeeds that Built from Scratch for 1 Container and Separate Container
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The following are the steps for using HPFeeds MongoDB for 1 container and separate container:

1.	To check the successful installation and to check the process we can type the command below for checking the hpfeeds process:

.. code-block:: RST

  $ supervisorctl status hpfeeds-broker

and the result can be similar to this:

.. image:: images/hpfeeds-checking-hpfeeds-process.png
   :width: 800

2.	To check the process of the mnemosyne we can type the command below:

.. code-block:: RST

  $ supervisorctl status mnemosyne

.. image:: images/hpfeeds-checking-mnemosyne-process.png
   :width: 800

3.	Then we should install pymongo to be able to run the add_user.py

.. code-block:: RST

  $ pip install pymongo

4.	 To add the ident and secret we can do the command below for each honeypot there is different channel, for now we will be focussing on dionaea Honeypot.

.. code-block:: RST

  $ python /opt/hpfeeds/broker/add_user.py sensor-dionaea(according to ident at hpfeeds.yaml) password1234(according to secret at hpfeeds.yaml) "mwbinary.dionaea.sensorunique,dionaea.capture,dionaea.capture.anon,dionaea.captures,dionaea.connections" " "

.. image:: images/hpfeeds-dionea-command.png
   :width: 800

5.	Then we need to add the python script that is monitoring the MongoDB and send the JSON data when the data arrived at MongoDB

.. code-block:: RST

  $ nano py123.py

And add this following python script: 

Modify the URL into the URL of your Node-RED IP 

.. code-block:: RST

  from pymongo import Connection
  import time
  import requests
  import json
  url = 'http://192.168.1.100:1880/test'
  db = Connection().mnemosyne
  coll = db.hpfeed
  cursor = coll.find(tailable=True)
  while cursor.alive:
  	try:
      	doc = cursor.next()
      	test = json.dumps(doc, indent=4, default=str)
      	print (test)
      	response = requests.post(url, data=test)
  	except StopIteration:
      	time.sleep(1)

6.	Then we need to execute the python script by typing:

.. code-block:: RST

  $ python3 py123.py

7.	Then at the dionaea we should modify the hpfeed Edit the hpfeeds.yaml at /opt/dionaea/etc/dionaea/ihandlers-available:

.. code-block:: RST

  $ nano /opt/dionaea/etc/dionaea/ihandlers-available/hpfeeds.yaml

.. image:: images/hpfeeds-edit-hpfeeds.yaml.png
   :width: 600

Edit it like this, after that copy the hpfeeds.yaml to /opt/Dionaea/etc/Dionaea/ihandlers-enabled.

.. code-block:: RST

  $ cd /opt/dionaea/etc/dionaea/ihandlers-available/hpfeeds.yaml /opt/dionaea/etc/dionaea/ihandlers-enabled

8.	Restart the Dionaea and the result will be like this:

.. image:: images/hpfeeds-restart-dionea.png
   :width: 800

9.	We do the attack via ftp to Dionaea and the result will be:

.. image:: images/hpfeeds-attack-via-ftp-to-dionea.png
   :width: 800

10.	The result of MongoDB should be like this:

.. image:: images/hpfeeds-result-in-mongodb.png
   :width: 800
