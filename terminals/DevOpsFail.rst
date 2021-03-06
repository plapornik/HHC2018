Dev Ops Fail
============



Terminal Challenge
------------------

.. image:: /images/devopsMOTD.png
                                                   
Our Solution
------------

First we used ``ls -a`` and saw that there was the directory ``kcconfmgmt`` and ``runtoanswer``.
We then used ``cd kcconfmgmt/`` and then ``ls -a`` to ensure that ``.git`` was in that directory.
We then run the command ``git log`` to recover the ID and message of each commit. One commit was particularly interesting.

.. code-block:: none
 :emphasize-lines: 50-55

 elf@414858ca95cb:~/kcconfmgmt$ git log
 commit 7b93f4be7e7b50b044739e02fa7c75b8fad32366
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Wed Nov 14 04:46:12 2018 -0500

     Add palceholder index, login, profile, signup pages while I CONTINUE TO WAIT FOR UX

 commit 20c7def24307589194b7dc05cd852552c36b2b2a
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Tue Nov 13 10:18:08 2018 -0500

     Add Bower setup for front-end

 commit 604e434713b4659d7f10b91ab6d20dfa58030c24
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Mon Nov 12 13:04:08 2018 -0500

     Add temp placeholders for login, profile, signup pages -- WAITING ON YOU UX TEAM

 commit 31f4eaec30df0f41fc700532d7bc2f6aac94deb8
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Mon Nov 12 00:51:23 2018 -0500

     Add routes for login, logout, signup, isLoggedIn, profile access

 commit ac32750bf6a4979bf37108f4438bc9695189ce14
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Sun Nov 11 15:30:15 2018 -0500

     Update index route for passport

 commit d84b728c7d9cf7f9bafc5efb9978cd0e3122283d
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Sat Nov 10 19:51:52 2018 -0500

     Add user model for authentication, bcrypt password storage

 commit c27135005753f6dde3511a7e70eb27f92f67393f
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Sat Nov 10 08:11:40 2018 -0500

     Add passport config

 commit a6449287cf9ed9151d94fb747f6904158c2c4d71
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Fri Nov 9 14:08:04 2018 -0500

     Add passport middleware for user auth

 commit 60a2ffea7520ee980a5fc60177ff4d0633f2516b
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Thu Nov 8 21:11:03 2018 -0500

     Per @tcoalbox admonishment, removed username/password from config.js, 
     default settings in config.js.def need to be updated before use

 commit b2376f4a93ca1889ba7d947c2d14be9a5d138802
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Thu Nov 8 13:25:32 2018 -0500

     Add passport module

 commit d99d465d5b9711d51d7b455584af2b417688c267
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Wed Nov 7 16:57:41 2018 -0500

     Correct typos, runs now! Change port for MongoDB connection

 commit 68405b8a6dcaed07c20927cee1fb6d6c59b62cc3
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Tue Nov 6 17:26:39 2018 -0500

     Add initial server config

 commit 69cc84998e57f4fc6aca17f2a5cb9caff53f3fd3
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Mon Nov 5 20:17:51 2018 -0500

     Added speakers.js data model

 commit c3ee078d17a5309fbe18426c048a9a12b495f39f
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Mon Nov 5 01:27:11 2018 -0500

     File reorganization under server/

 commit b4d783d7a7f8ba9bb3aee72aeba43ba9bb99c8b0
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Sun Nov 4 04:30:39 2018 -0500

     Module cleanup

 commit 9c06c0441c95323e8270f6a219439daba59017f5
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Fri Nov 2 11:05:49 2018 -0400

     Added Express EJS setup (go away, Jade)

 commit 1f9bbf6d2cee75a9dd6bb483edf940f9bb71035f
 Author: Sparkle Redberry <sredberry@kringlecon.com>
 Date:   Thu Nov 1 11:30:50 2018 -0400

We observed that ``commit 60a2ffea7520ee980a5fc60177ff4d0633f2516b`` was the commit in which the password was deleted.
We then run ``git diff b2376f4a93ca1889ba7d947c2d14be9a5d138802`` to look at the differences (changes) from the commit before tha commit onwards.

We observe that the following was deleted (- sign and shown in red):

.. code-block:: none

 -    'url' : 'mongodb://sredberry:twinkletwinkletwinkle@127.0.0.1:10073/node-api'

Therefore, the password may be ``twinkletwinkletwinkle`` which is the answer to this challenge.

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **Finding Passwords in Git**
 From: Sparkle Redberry
 `Search Git for Passwords <https://en.internetwache.org/dont-publicly-expose-git-or-how-we-downloaded-your-websites-sourcecode-an-analysis-of-alexas-1m-28-07-2015/https://en.internetwache.org/dont-publicly-expose-git-or-how-we-downloaded-your-websites-sourcecode-an-analysis-of-alexas-1m-28-07-2015/>`_

.. parsed-literal::
 **Git Cheat Sheet**
 From: Sparkle Redberry
 `Git Cheat Sheet <https://gist.github.com/hofmannsven/6814451>`_

Hints for Kringlecon :doc:`Question 7 </objectives/question7>`
--------------------------------------------------------------

.. parsed-literal::
 **CSV Injection Talk**
 From: Sparkle Redberry
 Somehow `Brian Hostetler is giving a talk on CSV injection <https://youtu.be/Z3qpcKVv2Bg>`_  WHILE he's giving a talk on Trufflehog. Whatta' guy!

.. parsed-literal::
 **OWASP on CSV Injection**
 From: Sparkle Redberry
 `OWASP CSV Injection Page <https://www.owasp.org/index.php/CSV_Injection>`_

