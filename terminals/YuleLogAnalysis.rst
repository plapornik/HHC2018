Yule Log Analysis
=================

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

