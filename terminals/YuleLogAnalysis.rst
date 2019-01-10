Yule Log Analysis
=================



Terminal Challenge
------------------

.. image:: /images/yuleMOTD.png

.. code-block:: none


                              .;:cccckkxdc;.                                     
                          .o0xc;,,,,,XMMMMMkc:,.                                 
                        lXMMMX;,,,,,,XMMMMK,,coddcclOkxoc,.                      
                      lk:oNMMMX;,,,,,XMMWN00o:,,,,,:MMMMMMoc;'                   
                    .0l,,,,dNMMX;,,,,XNNWMMMk,,,,,,:MMMMMx,,,,:;.                
                   .K;,,,,,,,xWMX;,,;Kx:kWMMMk,,,,,:MMMM0,,,,,,,:k'              
                  .XklooooddolckWN:l0:,,,;kWMMO,,,,:MMMN;,,,,,cOWMMd             
                ;oooc;,,,cMMMMMMxkO0,,,,,,,:OMM0,,,:MMWc,,,,lKMMMMWKo            
             ;OMMWl,,,,,,cMMMMMO,,,:cc,,,,,,,:0M0,,:MMd,,,oXMMWKxc,,,c           
           cOdXMMMWl,,,,,cMMMMX,,,,,,,:xxo:,,,,cK0,:MO,;xNWKxc,,,,,,,:.          
         .0l,,,oNMMWl,,,,cMMMW:,,,,,,dXMWNMWXOdc;lxcX:xOxc,,,,,,,,,,,,:          
        ,0;,,,,,,dNMWo,,,cMMMl,,,,;xNMMMMW0kkkkkkddxdddxxxxxxxxxxxxxxxo          
       .Wl,,,,,,,,,dWMo,,cMMx,,,:OWMMW0xc,:c,,:dOkcK:kc:ok0NMMMMMMMMMMd          
       KMMWXOdl;,,,,;xWd,cM0,,l0MW0dc,,,,,,lkWWk:,OW,:XO:,,,;ldOXWMMMM'          
      'MMMMMMMMMN0ko:,;kdcN;o00dc,,,,,,,,,,,0x;,,oMW,,;XWk;,,,,,,,:okk           
      cNKKKKKKKKKKKKKKkoodxxdccccccccccccccco,,,:WMW,,,;XMWk;,,,,,,,l            
      :x,,,,,,,,,,,,,cdkoOldldOKWMMMMMMMMMMMx,,,XMMW,,,,;XMMWx,,,,;c             
      .K,,,,,,,,,cd0WKl,xN,oXo,,,:ok0NMMMMMMc,,OMMMW,,,,,;KMMMNd;l'              
       dl,,,,cx0WMM0c,,lMN,,oMXl,,,,,,;ldOX0',dMMMMW,,,,,,;KMMMK;                
        OoxKWMMMWk:,,,;NMN,,,lWMKc,,,,,,,,ldclWMMMMW,,,,,,:oOl.                  
         OMMMMNx;,,,,,KMMN,,,,lWMM0c,,,,,l. .,cdkO00ccc:;,.                      
          cWXo,,,,,,,kMMMN,,,,,cWMMM0:,c:                                        
           .Kc,,,,,,:MMMMN,,,,,,dMMMMWk'                                         
 
 I am Pepper Minstix, and I'm looking for your help.
 Bad guys have us tangled up in pepperminty kelp!
 "Password spraying" is to blame for this our grinchly fate.
 Should we blame our password policies which users hate?

 Here you'll find a web log filled with failure and success.
 One successful login there requires your redress.
 Can you help us figure out which user was attacked?
 Tell us who fell victim, and please handle this with tact...

   Submit the compromised webmail username to 
   runtoanswer to complete this challenge.
 elf@711366eb1217:~$ 

Our Solution
------------

First, we run the ``ls`` command with the following output:

.. code-block:: none

 evtx_dump.py  ho-ho-no.evtx  runtoanswer

According to Pepper Minstix,there's a Python script that can help you convert it into XML for easier grep'ing
Thus, we used ``python evtx_dump.py ho-ho-no.evtx |grep -A20 '4625'`` to display all failed logins. We did this because password spraying would also have failed attempts and to look at the record structure.

.. code-block:: none
 :emphasize-lines: 1,7,21

 <EventID Qualifiers="">4625</EventID>
 <Version>0</Version>
 <Level>0</Level>
 <Task>12544</Task>
 <Opcode>0</Opcode>
 <Keywords>0x8010000000000000</Keywords>
 <TimeCreated SystemTime="2018-09-10 12:41:50.900736"></TimeCreated>
 <EventRecordID>234488</EventRecordID>
 <Correlation ActivityID="{71a9b66f-4900-0001-a8b6-a9710049d401}" RelatedActivityID=""></Correlatio
 n>
 <Execution ProcessID="664" ThreadID="712"></Execution>
 <Channel>Security</Channel>
 <Computer>WIN-KCON-EXCH16.EM.KRINGLECON.COM</Computer>
 <Security UserID=""></Security>
 </System>
 <EventData><Data Name="SubjectUserSid">S-1-5-18</Data>
 <Data Name="SubjectUserName">WIN-KCON-EXCH16$</Data>
 <Data Name="SubjectDomainName">EM.KRINGLECON</Data>
 <Data Name="SubjectLogonId">0x00000000000003e7</Data>
 <Data Name="TargetUserSid">S-1-0-0</Data>
 <Data Name="TargetUserName">sparkle.redberry</Data>
 <Data Name="TargetDomainName">EM.KRINGLECON</Data>

The password spraying would generate a lot of failed attempts over a short timeframe.
Using ``python evtx_dump.py ho-ho-no.evtx |grep -A20  "4625" |grep 'Time'`` we observed that most failed attempts were between 13:03 and 13:05.

We then used ``python evtx_dump.py ho-ho-no.evtx |grep -A20 -B20  "4624" |grep -A15 ' 13:0' |grep 'TargetUserName'`` to find any successful logins in that timeframe and found that the only successful login was that of **minty.canycane** which was the answer to the challenge.

| To make the output easier to read we used ``|grep -v 'Health'`` to ignore any HealthMailBox records.
| In grep -B is for lines before and -A is for lines after.

.. note::
 The eventID was the http code for that event.
 4624 stands for successful login and 4625 stands for failed login.
 Therefore we used this to filter failed and successful attempts respectfully



Hints for the Terminal Challenge
--------------------------------

Pepper Minstix says:

.. highlights::
 "Parsing through .evtx files can be tricky, but there's a Python script that can help you convert it into XML for easier grep'ing"

And gives the following hint:

.. parsed-literal::
 **Password Spraying**
 From: Pepper Minstix
 `Password Spraying with MailSniper.ps1 <https://securityweekly.com/2017/07/21/tsw11/>`_

Hints for Kringlecon Objective 6
--------------------------------

.. parsed-literal::
 **Barcode Creation**
 From: Pepper Minstix
 `Creating QR barcodes <https://www.the-qrcode-generator.com/>`_

.. parsed-literal::
 **SQL Injection**
 From: Pepper Minstix
 `SQL Injection <https://www.owasp.org/index.php/SQL_Injection_Bypassing_WAF#Auth_Bypass>`_




