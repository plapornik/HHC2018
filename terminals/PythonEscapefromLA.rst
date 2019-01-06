Python Escape from LA
=====================



Terminal Challenge
------------------

.. code-block:: Python

                :lllllllllllllllllllllllllllllllllllllllll,                      
                'lllllllllllllllllllllllllllllllllllllllll:                      
                 clllllllllllllllllllllllllllllllllllllllll.                     
                 'lllllllllllllllllllllllllllllllllllllllll:                     
                  ;lllllllllllllllllllllllllllllllllllllllll,                    
                   :lllllllllllllllllllllllllllllllllllllllll.                   
                    :lllllllllllllllllllllllllllllllllllllllll.                  
                     ;lllllllllllllllllllllllllllllllllllllllll'                 
                      'lllllllllllllllllllllllllllllllllllllllll;                
                       .cllllllllllllllllllllllllllllllllllllllllc.              
                       .:llllllllllllllllllllllllllllllllllllllllllc,.           
                    .:llllllllllllllllllllllllllllllllllllllllllllllll;.         
                 .,cllllllllllllllllllllllllllllllllllllllllllllllllllll,        
               .;llllllllllllllllllllllllllllllllllllllllllllllllllllllllc.      
              ;lllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc.     
            'llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc     
           :lllllll:..,..'cllllllllllllllllllllllc'.,'.'clllllllllllllllllll;    
         .clllllll'  :XK.  :llllllllllllllllllll;  ,XX.  ;lllllllllllllllllll.   
        .cllllllll.  oXX'  ,llllllllllllllllllll.  cXX;  .lllllllllllllllllll'   
        clllllllll;  .xl  .cllllllllllllllllllllc.  do  .clllllllllllllllllll,   
       :llllllllllll;'..':llllllllllllllllllllllll:'..':lllllllllllllllllllll'   
      .llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll.   
      ;lllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc    
      clllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllll.    
      cllllllllllllllllllllllllll..;lc..:llllllllllllllllllllllllllllllllll;     
      :lllllllllllllllllllllllll:  .l,  .lllllllllllllllllllllllllllllllll:      
      ,lllllllllllllllllllllllllc  .l;  ,llllllllllllllllllllllllllllllll:       
      .llllllllllllllllllllllllllc;lll::llllllllllllllllllllllllllllllll,        
       'llllllllllllllllllllllllllllllllllllllllllllllllllllllllllllllc.         
        ,llllllllllllllllllllllllllllllllllllllllllllllllllllllllllll,           
         'llllllllllllllllcccccccc;',.,clllllllllllllllllllllllllll,             
          .cllllllc:::::;;,,,,'...':c:;...'',,;;;::::::lllllllllc,               
            'cllllc::;::::cccccccccllc,,,,,,,'',:::::::lllllll;.                 
              .:llllllllllkMMMMMMMMMdlclllllllllollllllllll;.                    
                .':lllllllXMMMMMMMMMoloWMMMMMMMMXllllll:,.                       
                    .,:llccccccccccllllXMMMMMMMMWl:;'.                           
                        .,,,,,,,,,,clll:::::::::;                                
                       'lllllllllc.    ',,,,,,,,.                                
                      lMMMMMMMMMW,    .ddddddddd.                                
                     kMMMMMMMMMX.     kMMMMMMMMK                                 
                    ':::::::::,      .NWWWWWWWW:                                 
                   ',,,,,,,,,.       .,,,,,,,,'                                  
                 .oooooooooo.        ',,,,,,,,.                                  
                .NMMMMMMMMW;        cOOOOOOOOx                                   
                0MMMMMMMMMc         NMMMMMMMMk                                   
                ;;;;;;;;;'         .KKKKKKKKK:                                   
               .,,,,,,,,,           ,,,,,,,,,.                                   
               .ddddddddo           ',,,,,,,,.                                   
                XMMMMMMMN           cKKKKKKKKK.                                  
     .;:::;;,,,,,:ldddddd.           0MMMMMMMMX.       ....                      
       .,:ccccccccccccccc            'cccccccccc:::ccccc;.                       
          .:ccccccccccccc            .ccccccccccccccc:'.                         
            .;;;;;;;;;;;;            .ccccccccccccc;.                            
                                     ..............                              
                                                                                
                                                                                


 I'm another elf in trouble,
 Caught within this Python bubble.

 Here I clench my merry elf fist -
 Words get filtered by a black list!

 Can't remember how I got stuck,
 Try it - maybe you'll have more luck?

 For this challenge, you are more fit.
 Beat this challenge - Mark and Bag it!

 -SugarPlum Mary

 To complete this challenge, escape Python
 and run ./i_escaped
 >>> 

Our Solution
------------

First we tried running ``os.system("./i_escaped")`` but found that ``Use of the command os.system is prohibited for this question``
We then tried to run ``import``, ``exec``, ``eval``, and ``compile``.
The results were as follows:

.. code-block:: python

 >>> import
 Use of the command import is prohibited for this question.
 >>> exec
 Use of the command exec is prohibited for this question.
 >>> eval
 <built-in function eval>
 >>> compile
 Use of the command compile is prohibited for this question.

Thus, we can use ``eval``.
We then tried to run ``eval('os.sys'+'tem("./i_escaped")')`` but got a ``NameError: name 'os' is not defined``.

Thus, we used ``os=eval('__im'+'port__("os"))`` to import the os as a parameter (os)

Then, we used ``eval('os.sys'+'tem("./i_escaped")')`` to run ./i_escaped and solve the challenge.




Hints for the Terminal Challenge
--------------------------------

.. parsed-literal::
 **Python Escape**
 From: SugarPlum Mary
 Check out Mark Baggett's talk upstairs

Hints for Kringlecon Objective 8
--------------------------------

.. parsed-literal::
 **HTTP/2.0 Intro and Decryption**
 From: SugarPlum Mary
 Did you see Chris' & Chris' talk on HTTP/2.0?