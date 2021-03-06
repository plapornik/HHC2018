Python Escape from LA
=====================

Terminal Challenge
------------------

.. image:: /images/pythonescapeMOTD.png

Our Solution
------------

First we tried running ``os.system("./i_escaped")`` but found that ``Use of the command os.system is prohibited for this question``
We then tried to run ``import``, ``exec``, ``eval``, and ``compile``.
The results were as follows:

.. code-block:: python

 >>> import
 Use of the command import is prohibited for this question.
 >>> exec
 Use of the command exec is prohibited for this question.
 >>> eval
 <built-in function eval>
 >>> compile
 Use of the command compile is prohibited for this question.

Thus, we can use ``eval``.
We then tried to run ``eval('os.sys'+'tem("./i_escaped")')`` but got a ``NameError: name 'os' is not defined``.

Thus, we used ``os=eval('__im'+'port__("os"))`` to import the os as a parameter (os)

Then, we used ``eval('os.sys'+'tem("./i_escaped")')`` to run ./i_escaped and solve the challenge.

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **Python Escape**
 From: SugarPlum Mary
 Check out `Mark Baggett's talk <https://youtu.be/ZVx2Sxl3B9c>`_ upstairs

Hints for Kringlecon :doc:`Question 8 </objectives/question8>`
--------------------------------------------------------------

.. parsed-literal::
 **HTTP/2.0 Intro and Decryption**
 From: SugarPlum Mary
 Did you see `Chris' & Chris' talk <https://youtu.be/9E-8HkDs-kQ>`_ on HTTP/2.0?

.. parsed-literal::
 **Conversation with SugarPlum Mary**

 Yay, you did it! You escaped from the Python!

 As a token of my gratitude, I would like to share a rumor I had heard about Santa's new web-based packet analyzer - Packalyzer.

 Another elf told me that Packalyzer was rushed and deployed with development code sitting in the web root.

 Apparently, he found this out by looking at HTML comments left behind and was able to grab the server-side source code.

 There was suspicious-looking development code using environment variables to store SSL keys and open up directories.

 This elf then told me that manipulating values in the URL gave back weird and descriptive errors.

 I'm hoping these errors can't be used to compromise SSL on the website and steal logins.

 On a tooootally unrelated note, have you seen the HTTP2 talk at at KringleCon by the Chrises? I never knew HTTP2 was so different!
 