Question 5: AD Privilege Discovery
==================================

| Using the data set contained in this `SANS Slingshot Linux image <https://download.holidayhackchallenge.com/HHC2018-DomainHack_2018-12-19.ova>`_, find a reliable path from a Kerberoastable user to the Domain Admins group. What’s the user’s logon name (in username@domain.tld format)? Remember to avoid RDP as a control path as it depends on separate local privilege escalation flaws. For hints on achieving this objective, please visit Holly Evergreen and help her with the :doc:`CURLing Master </terminals/CURLingMaster>` Cranberry Pi terminal challenge.

Answer: *LDUBEJ00320@AD.KRINGLECASTLE.COM*

Solution
--------

We downloaded the Linux image and imported it into **VMware Workstation 15 Player**.

After running the virtual machine we started the **BloodHound** application.

As shown below, we clicked on the **More Info** button then clicked on **Queries** and selected **Shortest Paths to Domain Admins from Kerberoastable Users**

.. image:: /images/BloodHoundQuery.png

In the dialog window that popped up, we selected the **DOMAIN ADMINS@AD.KRINGLECASTLE.COM** Domain Admins group.

We then clicked on the **Settings** button and changed **Node Label Display** to **Always Display**.

This displayed the following graph:

.. image:: /images/question5_ShortestPath.png

The user's login name that we needed was **LDUBEJ00320@AD.KRINGLECASTLE.COM**
