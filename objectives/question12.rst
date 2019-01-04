Question 12: Recover Alabaster's Password
=========================================

| After activating the kill-switch domain in the last question, Alabaster gives you `a zip file <https://www.holidayhackchallenge.com/2018/challenges/forensic_artifacts.zip>`_ with a memory dump and encrypted password database. Use these files to decrypt Alabaster's password database. What is the password entered in the database for the Vault entry?

Answer: *ED#ED#EED#EF#G#F#G#ABA#BA#B*

Solution
--------

The following 

The next step is to try and work out what type of file the ``alabaster_passwords.elfdb`` is. We found the simplest way to work this out was to open the file in a text editor and look at the initial characters. This provides the string ``SQLite format 3``. 

.. note::
 A quick examination of the file using a text editor revealed a couple of passwords but the parts that were important consisted of binary data.

We didn't have SQLite handily available so we simply opened the file in an online `SQLite application <https://sqliteonline.com>`_.

The database showing the contents of the passwords table is shown in the following figure. The password for the vault has been highlighted.

.. image:: /images/alabaster_passwords.elfdb.png

.. note::
 We tried using the passwords with online services such as NetFlix, but found out that the accounts did not exist. Perhaps Santa has his own regionalised versions of these online services.