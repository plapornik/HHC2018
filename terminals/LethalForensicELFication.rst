Lethal ForensicELFication
=========================



Terminal Challenge
------------------
.. code-block:: none

                         ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
                        ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
    .,.   ,. .......,.  .',..'',,..:::::,,;:c:::ccooooodxkkOOkOO0KKXXXNNWMMMMMMM
    ldd: .d' ';... .o:  .d;.;:....'dl,;do,:lloc:codddodOOxxk0KOOKKKKXNNNWMMMMMMM
    lo.ol.d' ';'..  ,d'.lc..;:,,,.'docod:,:l:locldlddokOxdxxOK0OKKKXXXNNWMMMMMMM
    lo  lod' ';      co:o...;:....'dl':dl,:l::oodlcddoxOkxxk0KOOKKKKXNNNWMMMMMMM
    ,,   ,;. ......  .;:....',,,,''c:'':l;;c:;:llccoooodkkOOOkOO0KKKXNNNWMMMMMMM
                        ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
                        ............'''',,,;;;::ccclloooddxxkkOO00KKXXNNWWMMMMMM
 Christmas is coming, and so it would seem,
 ER (Elf Resources) crushes elves' dreams.
 One tells me she was disturbed by a bloke.
 He tells me this must be some kind of joke.
 Please do your best to determine what's real.
 Has this jamoke, for this elf, got some feels?
 Lethal forensics ain't my cup of tea;
 If YOU can fake it, my hero you'll be.
 One more quick note that might help you complete,
 Clearing this mess up that's now at your feet.
 Certain text editors can leave some clue.
 Did our young Romeo leave one for you?
 - Tangle Coalbox, ER Investigator
   Find the first name of the elf of whom a love poem 
   was written.  Complete this challenge by submitting 
   that name to runtoanswer.
 elf@a90c43716d57:~$ 

Our Solution
------------

When using the command ``ls`` only ``runtoanswer`` appears. Thus, we used the command ``ls -a`` to display all items even the ones beginning with a '.' character.

.. code-block:: none

 .  ..  .bash_history  .bash_logout  .bashrc  .profile  .secrets  .viminfo  runtoanswer

Thus we can see there's a vim artifact called .viminfo

Using the command ``cat .viminfo`` and then through then scrolling through the document, we found the following info:

.. code-block:: none

 # Search String History (newest to oldest):
 ? Elinore
 |2,1,1536607217,,"Elinore"
 ? God
 |2,1,1536606833,,"God"
 ? rousted
 |2,1,1536605996,,"rousted"
 ? While
 |2,1,1536604909,,"While"
 ? studied
 |2,1,1536602549,,"studied"
 ? sound
 |2,1,1536600579,,"sound"

The only female name in the string history is Elinore, which is the answer to this challenge.

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **Vim Artifacts**
 From: Tangle Coalbox
 `Forensic Relevance of Vim Artifacts <https://tm4n6.com/2017/11/15/forensic-relevance-of-vim-artifacts/>`_

Hints for KringleCon Objective 3
--------------------------------

.. parsed-literal::
 **Opening a Ford Lock Code**
 From: Tangle Coalbox
 `Opening a Ford with a Robot and the de Bruijn Sequence <https://hackaday.com/2018/06/18/opening-a-ford-with-a-robot-and-the-de-bruijn-sequence/>`_

.. parsed-literal::
 **de Bruijn Sequence Generator**
 : Tangle Coalbox
 `de Bruijn sequence generator <http://www.hakank.org/comb/debruijn.cgi>`_