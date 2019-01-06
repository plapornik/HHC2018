Stall Mucking Report
====================



Terminal Challenge
------------------

.. image:: /images/StallMuckingReportMOTD.png

Our Solution
------------

We tried to solve this challenge by using different variations of the ``ps`` command.
``ps`` didn't work therefore we tried ``ps -a``. When this didn't work we tried ``ps -t``. This did show the credentials we were looking for, but they were cut at the edge of the terminal.
Thus we used ``|less`` and found ``directreindeerflatterystable`` to be the most probable password for the user ``report-upload``.

.. code-block:: none
 :emphasize-lines: 11,12

   PID TTY      STAT   TIME COMMAND
     1 pts/0    Ss     0:00 /bin/bash /sbin/init
    11 pts/0    S      0:00 sudo -u manager /home/manager/samba-wrapper.sh --verbosity=none --no-ch
 eck-certificate --extraneous-command-argument --do-not-run-as-tyler --accept-sage-advice -a 42 -d~
  --ignore-sw-holiday-special --suppress --suppress //localhost/report-upload/ directreindeerflatte
 rystable -U report-upload
    12 pts/0    S      0:00 sudo -E -u manager /usr/bin/python /home/manager/report-check.py
    16 pts/0    S      0:00 sudo -u elf /bin/bash
    17 pts/0    S      0:00 /bin/bash /home/manager/samba-wrapper.sh --verbosity=none --no-check-ce
 rtificate --extraneous-command-argument --do-not-run-as-tyler --accept-sage-advice -a 42 -d~ --ign
 ore-sw-holiday-special --suppress --suppress //localhost/report-upload/ directreindeerflatterystab
 le -U report-upload
    18 pts/0    S      0:00 /usr/bin/python /home/manager/report-check.py
    19 pts/0    S      0:00 sleep 60
    20 pts/0    S      0:00 /bin/bash
    30 pts/0    R+     0:00 ps -t
    31 pts/0    S+     0:00 less
 (END)

Then, we used the command ``smbclient //localhost/report-upload/ -U report-upload`` so that we can add items to the samba share.
After entering the password, we used ``put report.txt`` to upload the report.

.. code-block:: none

 elf@4973850de893:~$ smbclient //localhost/report-upload/ -U report-upload
 WARNING: The "syslog" option is deprecated
 Enter report-upload's password: 
 Domain=[WORKGROUP] OS=[Windows 6.1] Server=[Samba 4.5.12-Debian]
 smb: \> put report.txt

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **Plaintext Credentials in Commands**
 From: Wunorse Openslae
 `Keeping Command Line Passwords Out of PS <https://blog.rackspace.com/passwords-on-the-command-line-visible-to-ps>`_

Hints for KringleCon Objective 4
--------------------------------
Wunorse Openslae hints to using the entropy=True option when running Trufflehog
Two hints are also unlocked in the badge:

.. parsed-literal::
 **Trufflehog Tool**
 From: Wunorse Openslae
 `Trufflehog <https://github.com/dxa4481/truffleHog>`_

.. parsed-literal::
 **Trufflehog Talk**
 From: Wunorse Openslae
 `Brian Hostetler is giving a great Trufflehog talk <https://youtu.be/myKrWVaq3Cw>`_ upstairs



