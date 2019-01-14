Sleigh Bell Lottery
===================

.. image:: /images/sleighbellMOTD.png

Our Solution
------------

Using the hint as a starting point, we ran the following command to find functions that we could call::

 nm sleighbell-lotto

The output consisted of the following:

.. code-block:: none
 :linenos:
 :emphasize-lines: 44,59

                  U EVP_sha256@@OPENSSL_1_1_0
                  U HMAC@@OPENSSL_1_1_0
 0000000000207d40 d _DYNAMIC
 0000000000207f40 d _GLOBAL_OFFSET_TABLE_
 0000000000001630 R _IO_stdin_used
                  w _ITM_deregisterTMCloneTable
                  w _ITM_registerTMCloneTable
 000000000000702c r __FRAME_END__
 0000000000006dcc r __GNU_EH_FRAME_HDR
 0000000000208068 D __TMC_END__
 0000000000208068 B __bss_start
                  w __cxa_finalize@@GLIBC_2.2.5
 0000000000208000 D __data_start
 0000000000000ac0 t __do_global_dtors_aux
 0000000000207d38 t __do_global_dtors_aux_fini_array_entry
 0000000000208008 D __dso_handle
 0000000000207d30 t __frame_dummy_init_array_entry
                  w __gmon_start__
 0000000000207d38 t __init_array_end
 0000000000207d30 t __init_array_start
 0000000000001620 T __libc_csu_fini
 00000000000015b0 T __libc_csu_init
                  U __libc_start_main@@GLIBC_2.2.5
                  U __stack_chk_fail@@GLIBC_2.4
 0000000000208068 D _edata
 0000000000208080 B _end
 0000000000001624 T _fini
 00000000000008c8 T _init
 0000000000000a00 T _start
 0000000000000c1e T base64_cleanup
 0000000000000c43 T base64_decode
 0000000000000bcc T build_decoding_table
 0000000000208068 b completed.7696
 0000000000208000 W data_start
 0000000000208070 B decoded_data
 0000000000208078 b decoding_table
 0000000000000a30 t deregister_tm_clones
 0000000000208020 d encoding_table
                  U exit@@GLIBC_2.2.5
 0000000000000b00 t frame_dummy
                  U free@@GLIBC_2.2.5
                  U getenv@@GLIBC_2.2.5
 0000000000000b0a T hmac_sha256
 00000000000014ca T main
                  U malloc@@GLIBC_2.2.5
                  U memcpy@@GLIBC_2.14
                  U memset@@GLIBC_2.2.5
                  U printf@@GLIBC_2.2.5
                  U puts@@GLIBC_2.2.5
                  U rand@@GLIBC_2.2.5
 0000000000000a70 t register_tm_clones
                  U sleep@@GLIBC_2.2.5
 00000000000014b7 T sorry
                  U srand@@GLIBC_2.2.5
                  U strlen@@GLIBC_2.2.5
                  U time@@GLIBC_2.2.5
 0000000000000f18 T tohex
 0000000000208060 D winnermsg
 0000000000000fd7 T winnerwinner

We opened the file for analysis in **gdb** using::

 ./gdb -q sleighbell-lotto

We created a breakpoint on **main** using::

 break main

We ran the program using::

 run

Once the program stopped, we jumped to the **winnerwinner** function using::

 jump winnerwinner

This resulted in the following:

.. image:: /images/sleighbell_winner.png

Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **Using gdb to Call Random Functions!**
 From: Shinny Upatree
 `Using gdb to Call Random Functions! <https://pen-testing.sans.org/blog/2018/12/11/using-gdb-to-call-random-functions>`_

Hints for Kringlecon :doc:`Question 9 </objectives/question9>`
--------------------------------------------------------------

**Conversation with Shiny Upatree**

| Sweet candy goodness - I win! Thank you so much!
| Have you heard that Kringle Castle was hit by a new ransomware called Wannacookie?
| Several elves reported receiving a cookie recipe Word doc. When opened, a PowerShell screen flashed by and their files were encrypted.
| Many elves were affected, so Alabaster went to go see if he could help out.
| I hope Alabaster watched the PowerShell Malware talk at KringleCon before he tried analyzing Wannacookie on his computer.
| An elf I follow online said he analyzed Wannacookie and that it communicates over DNS.
| He also said that Wannacookie transfers files over DNS and that it looks like it grabs a public key this way.
| Another recent ransomware made it possible to retrieve crypto keys from memory. Hopefully the same is true for Wannacookie!
| Of course, this all depends how the key was encrypted and managed in memory. Proper public key encryption requires a private key to decrypt.
| Perhaps there is a flaw in the wannacookie author's DNS server that we can manipulate to retrieve what we need.
| If so, we can retrieve our keys from memory, decrypt the key, and then decrypt our ransomed files.

