Question 9: Catch the Malware
=============================

| Alabaster Snowball is in dire need of your help. Santa's file server has been hit with malware. Help Alabaster Snowball deal with the malware on Santa's server by completing several tasks. For hints on achieving this objective, please visit Shinny Upatree and help him with the :doc:`Sleigh Bell Lottery </terminals/SleighBellLottery>` Cranberry Pi terminal challenge.
| To start, assist Alabaster by accessing (clicking) the snort terminal below:

.. image:: /images/snort.png

| Then create a rule that will catch all new infections. What is the success message displayed by the Snort terminal?

Answer: *Snort is alerting on all ransomware and only the ransomware!*

Solution
--------

Step 1: Analysing the packet captures
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We downloaded a packet capture from http://snortsensor1.kringlecastle.com/ and opened it using Wireshark. 

We noticed that the following string was repeated frequently within the DNS query and response packets: ``77616E6E61636F6F6B69652E6D696E2E707331``. We believed that this was associated with the malware we needed to stop.

We perform a HEX -> ASCII translation on the string to create the following table:

================= ============================================================================
Item              Value
================= ============================================================================
Decoded text      wannacookie.min.ps1
Decoded DNS query 77616E6E61636F6F6B69652E6D696E2E707331
================= ============================================================================

The ``wannacookie.min.ps1`` string confirmed that we were looking at the correct data.

Step 2: Analysing how Snort was configured
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We analysed the following files to determine how Snort was configured within the terminal:

* **snort.conf** - This file showed us that site specific rules were configured in the ``$RULE_PATH/local.rules`` file, where ``$RULE_PATH = /etc/snort/rules``
* **classification.config** - This file showed us the exisiting classifications. ``malware-cnc`` should be used for our rule.
* **local.rules** - This revealed that no local rules had previously been created

Step 3: Creating the Snort rule
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To create the rule we edited ``/etc/snort/rules/local.rules`` using **nano** and created the following rule:

.. code-block:: none

 alert udp any any <> any any (content: "77616E6E61636F6F6B69652E6D696E2E707331";msg:"MALWARE-CNC: wannacookie malware";sid:1000004;classtype:malware-cnc;)

* The rule matches UDP traffic in either direction with content that includes ``77616E6E61636F6F6B69652E6D696E2E707331``.
* The rule will log the following message: ``MALWARE-CNC: wannacookie malware``
* The rule utilises a custom signature ID of ``1000004``
* The rule utilises the ``malware-cnc`` classification

After saving the rule, we received the following terminal message: ``Congratulation! Snort is alerting on all ransomware and only the ransomware!``
