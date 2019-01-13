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

5. **$Pub_key_encrypted_Key** is a string a 512 hexadecimal digits.

6. The symmetric key gets hashed using SHA1. The hash has a length of 40 and consists of HEX digits.

Step 2: Obtain the private key
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Based on **wannacookie.ps1** we created the following code to download the RSA key used to encrypt the symmetric key and import it as a X509 certificate.

.. code-block:: powershell

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

We hoped that the certifcate would also contain the private key but examining **$cert.value** revealed that the certificate only contained the public key with no associated the private key.

Feeling inspired by something we saw in the **app.js** code from :doc:`Question 8 </objectives/question8>` we substituted ``server.crt`` in the above code with ``server.key``. After working through a few errors we ended up with the following code to obtain the private key:

.. code-block:: powershell

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

**$priv_key** was a string representing the private key in PEM format.

Step 3: Create a X509 certificate containing public/private keys
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Instead of trying to work out how to import utilise the private key with the System.Security.Cryptography .NET library, we decided to utilise **OpenSSL**, using the public/private keys that we already had, to create a X509 certifcate containing both private/public keys. Our aim was to later import this certificate using code based on ``wannacookie.ps1`` and decrypt the symmetric encryption key.

Extending the previous code we used to download the public key, we used the following code to export the public key (**$pub_key**) to a file called **kringle.cer**.

.. code-block:: powershell

 Set-Content -Path 'E:\Temp\kringle.cer' -Value $pub_key -Encoding Byte







The next step is to try and work out what type of file the ``alabaster_passwords.elfdb`` is. We found the simplest way to work this out was to open the file in a text editor and look at the initial characters. This provides the string ``SQLite format 3``. 

.. note::
 A quick examination of the file using a text editor revealed a couple of passwords but the parts of the file that were important consisted of binary data.

We didn't have SQLite handily available so we simply opened the file in an online `SQLite application <https://sqliteonline.com>`_.

The database showing the contents of the passwords table is shown in the following figure. The password for the vault has been highlighted.

.. image:: /images/alabaster_passwords.elfdb.png

.. note::
 We tried using the passwords with online services such as NetFlix, but found out that the accounts did not exist. Perhaps Santa has his own regionalised versions of these online services.
 