Lethal ForensicELFication
=========================

Terminal Challenge
------------------

.. image:: /images/elficationMOTD.png

Our Solution
------------
Using the hint as a starting point, our aim was to look at historical artifacts to see if any contained information about the elf.

The bash history obtained using the ``history`` command showed that the **vim** text editor was used but contained no information about the elf.

We then looked at the current directory. The ``ls`` command only returned the ``runtoanswer`` file. Thus, we used the command ``ls -a`` to display all items including lines beginning with a '.' character. The output was as follows:

.. code-block:: none

 .  ..  .bash_history  .bash_logout  .bashrc  .profile  .secrets  .viminfo  runtoanswer

We can see there's a vim artifact called .viminfo

After using the command ``cat .viminfo`` and scrolling through the output, we found the following infoformation:

.. code-block:: none
 :emphasize-lines: 2,3

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

Hints for KringleCon :doc:`Question 3 </objectives/question3>`
--------------------------------------------------------------

.. parsed-literal::
 **Opening a Ford Lock Code**
 From: Tangle Coalbox
 `Opening a Ford with a Robot and the de Bruijn Sequence <https://hackaday.com/2018/06/18/opening-a-ford-with-a-robot-and-the-de-bruijn-sequence/>`_

.. parsed-literal::
 **de Bruijn Sequence Generator**
 : Tangle Coalbox
 `de Bruijn sequence generator <http://www.hakank.org/comb/debruijn.cgi>`_

Additional fun within the terminal
----------------------------------

The poem is located at ``/home/elf/.secrets/her/poem.txt`` 