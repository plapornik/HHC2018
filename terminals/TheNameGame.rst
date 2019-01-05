The Name Game
=============

Minty Candycane provides hints for :doc:`Question 2 </objectives/question2>` after you help her with this Cranberry Pi terminal challenge.

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

Step 1: Perform a command injection to start a new shell
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After selecting option 2 the following appeared [#f1]_ :

.. code-block:: none

 Validating data store for employee onboard information.
 Enter address of server: 

We entered ``127.0.0.1`` and got the following output:

.. code-block:: none

 PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
 64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.042 ms
 64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.062 ms
 64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.061 ms

 --- 127.0.0.1 ping statistics ---
 3 packets transmitted, 3 received, 0% packet loss, time 2042ms
 rtt min/avg/max/mdev = 0.042/0.055/0.062/0.009 ms
 onboard.db: SQLite 3.x database
 Press Enter to continue...:

This is the same as the output from the ``ping`` command, suggesting that we might be able to perform a command injection.

We observed that if you type ``;ls`` instead of an IP address, the output listed information about files just the same as using ``ls`` in a shell.

We tried starting a new shell by typing ``;/bin/bash``. This did not work and took as back into the employee onboarding application.

.. note::
 We later found out that the reason that we couldn't start a new bash shell using ``;/bin/bash`` was that one of the entries in the ``.bashrc`` file included ``/usr/bin/pwsh menu.ps1`` which starts the employee onboarding application.

We then tried starting a new shell by typing ``/bin/sh`` and successfully opened a shell.

Step 2: Retrieve Mr Chan's information from the employee onboarding application
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Running ``ls -a`` showed the following output::

 .  ..  .bash_logout  .bashrc  .cache  .local  menu.ps1  onboard.db  .profile  runtoanswer

Using the hints provided by Minty Candycane, and the output from the above ``ping`` command, we had a good idea that the ``onboard.db`` file was a SQLite database. We confirmed this by entering ``file onboard.db``.

.. note::
  We later found out that the employee onboarding application also uses the same ``/bin/bash -c "/usr/bin/file onboard.db"`` command during option 2. 

We then entered ``sqlite3 onboard.db .dump | grep 'Chan'`` to get:

.. code-block:: none

 INSERT INTO "onboard" VALUES(84,'Scott','Chan','48 Colorado Way',NULL,'Los Angeles','90067','4017533509','scottmchan90067@gmail.com');

Therefore Mr Chan's first name is Scott.

We then had to run the ``runtoanswer`` application to enter the correct inormation and complete the challenge.

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **PowerShell Command Injection**
 From: Minty Candycane
 `PowerShell Call/& Operator <https://ss64.com/ps/call.html>`_

.. parsed-literal::
 **SQLite3 .dump'ing**
 From: Minty Candycane
 `SQLite3 Data Dump <https://www.digitalocean.com/community/questions/how-do-i-dump-an-sqlite-database>`_

Hints for KringleCon :doc:`Question 2 </objectives/question2>`
--------------------------------------------------------------

.. parsed-literal::
 **Finding Browsable Directories**
 From: Minty Candycane
 On a website, finding browsable directories is sometimes as simple as removing characters from the end of a URL.

.. parsed-literal::
 **Website Directory Browsing**
 From: Minty Candycane
 `Website Directory Browsing <https://portswigger.net/kb/issues/00600100_directory-listing>`_

Additional fun within the terminal
----------------------------------

An examination of the ``menu.ps1`` code showed the following:

.. code-block:: powershell 
 :emphasize-lines: 16,17,18

    do
    {
        Show-Menu
        $input = Read-Host 'Please make a selection'
        switch ($input)
        {
            '1' {
                cls
                Employee-Onboarding-Form
            } '2' {
                cls
                Write-Host "Validating data store for employee onboard information."
                $server = Read-Host 'Enter address of server'
                /bin/bash -c "/bin/ping -c 3 $server"
                /bin/bash -c "/usr/bin/file onboard.db"
            } '9' {
                /usr/bin/pwsh
                return
            } 'q' {
                return
            } default {
                Write-Host "Invalid entry."
            }
        }
        pause
    }
    until ($input -eq 'q')

Pressing the hidden option ``9`` opens a PowerShell shell.

.. rubric:: Extra Info
.. [#f1] After typing ``1`` we were taken to a registration page, which made us lose some time by trying to use that to solve the challenge. By typing ``q`` one cannot type anything more.
