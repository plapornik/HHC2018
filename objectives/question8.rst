Question 8: Network Traffic Forensics
=====================================

| Santa has introduced a `web-based packet capture and analysis tool <https://packalyzer.kringlecastle.com/>`_ to support the elves and their information security work. Using the system, access and decrypt HTTP/2 network activity. What is the name of the song described in the document sent from Holly Evergreen to Alabaster Snowball? For hints on achieving this objective, please visit SugarPlum Mary and help her with the :doc:`Python Escape from LA </terminals/PythonEscapefromLA>` Cranberry Pi terminal challenge.

Answer: *mary had a little lamb*

Solution
--------

The hints from SugarPlum Mary suggested that a good starting point was to look at the source code of the packalyzer site root. After browsing to https://packalyzer.kringlecon.com we used the ``Google Chrome -> View page source`` option and found the following comment:

.. code-block:: none

 //File upload Function. All extensions and sizes are validated server-side in app.js

As shown in the following figure, we also used ``Google Chrome -> Developer tools -> Source`` to determine that most of the scripts were accessible from http://packalyzer.kringlecastle.com/pub/

.. image:: /images/packalyzer_pub.png

After a couple of attempts we determined that the server-side code was accessible using ``https://packalyzer.kringlecastle.com/pub/app.js``

According to SugarPlum Mary, the file had *suspicious-looking development code using environment variables to store SSL keys and open up directories*. This was soon verified to be true. Of particular importance were the following snippets:

.. code-block:: javascript

 const dev_mode = true;
 const key_log_path = ( !dev_mode || __dirname + process.env.DEV + process.env.SSLKEYLOGFILE )

 key: fs.readFileSync(__dirname + '/keys/server.key'),
 cert: fs.readFileSync(__dirname + '/keys/server.crt'),

 keylog : key_log_path     //used for dev mode to view traffic. Stores a few minutes worth at a time

 if (dev_mode) {
    //Can set env variable to open up directories during dev
    const env_dirs = load_envs();
 } else {
    const env_dirs = ['/pub/','/uploads/'];
 }

.. tip::
 We lost quite a bit of time at this stage because we ignored that Linux is case-sensitive (``dev`` is not the same as ``DEV``).

We initially found that ``https://packalyzer.kringlecastle.com/SSLKEYLOGFILE/`` returned:

.. code-base:: none
  Error: ENOENT: no such file or directory, open '/opt/http2packalyzer_clientrandom_ssl.log/'





This elf then told me that manipulating values in the URL gave back weird and descriptive errors.