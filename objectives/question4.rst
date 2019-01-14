Question 4: Data Repo Analysis
==============================

| Retrieve the encrypted ZIP file from the `North Pole Git repository <https://git.kringlecastle.com/Upatree/santas_castle_automation>`_. What is the password to open this file? For hints on achieving this objective, please visit Wunorse Openslae and help him with :doc:`Stall Mucking Report </terminals/StallMuckingReport>` Cranberry Pi terminal challenge.

Answer: *Yippee-ki-yay*

Solution
--------

Step 1: Analysing the GIT repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Following the hints associated with this question, we used the **TruffleHog** application to analyse the git repository.

Using the ``trufflehog --regex https://git.kringlecastle.com/Upatree/santas_castle_automation.git | less`` command returned information that included the following:

.. code-block:: none
 :emphasize-lines: 11

 +Our Lead InfoSec Engineer Bushy Evergreen has been noticing an increase of brute force attacks in our logs. Furthermore, Albaster discovered and published a vulnerability with our password length at the last Hacker Conference.
 +
 +Bushy directed our elves to change the password used to lock down our sensitive files to something stronger. Good thing he caught it before those dastardly villians did!
 +
 + 
 +Hopefully this is the last time we have to change our password again until next Christmas. 
 +
 +
 +
 +
 +Password = 'Yippee-ki-yay'
 +
 +

The password we needed was **Yippee-ki-yay**

Step 2: Locating the ZIP file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clicking on the **Find File** button in the online git repository did not return any ZIP files.

We cloned the git repository using the  ``git clone https://git.kringlecastle.com/Upatree/santas_castle_automation.git`` command. Following this we used the ``find . -name *.zip`` command to return the ``./santas_castle_automation/schematics/ventilation_diagram.zip`` file path.

Inside the ``ventilation_diagram.zip`` file were diagrams for the :doc:`Google Ventilation Maze </appendices/GoogleVent>`.
