Documenting a walkthrough of the [Krypton](http://overthewire.org/wargames/krypton/) web challenge on OverTheWire.org.

This is mostly following the writeup by Matt Stiles [nsimattstiles.wordpress.com/2014/04/30/overthewire-krypton-level-0-5-writeup/](http://nsimattstiles.wordpress.com/2014/04/30/overthewire-krypton-level-0-5-writeup/).  
All of his solutions are shown in python.

I'm following the Markdown style guide at [daringfireball.net/projects/markdown/syntax](http://daringfireball.net/projects/markdown/syntax)

# Level 0 #

[overthewire.org/wargames/krypton/krypton0.html](http://overthewire.org/wargames/krypton/krypton0.html)

## The Challenge ##

> The following string encodes the password using Base64:

> S1JZUFRPTklTR1JFQVQ=

## Solutions ##

### Python ###

    #!/usr/bin/python
    import base64
 
    keyin = "S1JZUFRPTklTR1JFQVQ="
    keyout = base64.b64decode(keyin)
 
    print(keyout)
    
### PHP ###

    <?

    print base64_decode("S1JZUFRPTklTR1JFQVQ=");

    ?>
    
## Theory ##

Referencing Wikipedia article on [Base64](http://en.wikipedia.org/wiki/Base64).  See it for illustrations.

Created to convert binary data (8-bit) to a format that can be safely transported or stored through a text-only medium.

Idea is to take 3 bytes of data and lay it out as 24-bits, then divide this into 4 6-bit segments interpreted as numbers.  
These 64 possible 6-bit numbers are then assigned to be the standard text characters A-Z, a-z, 0-9, and + and /.  Equals signs are 
used to handle the case where the last block contains only one or two characters.

# Level 1 #

[overthewire.org/wargames/krypton/krypton1.html](http://overthewire.org/wargames/krypton/krypton1.html)

Use the password to log into the first level.

    ssh krypton1@krypton.labs.overthewire.org
    
The files are in /krypton.  

## The Challenge ##

> The password for level 2 is in the file ‘krypton2’.  **It is 'encrypted' using a simple rotation called ROT13.**
> It is also in non-standard ciphertext format. 
> **When using alpha characters for cipher text it is normal to group the letters into 5 letter clusters, regardless of word boundaries.** 
> This helps obfuscate any patterns. This file has kept the plain text word boundaries and carried them to the cipher text. Enjoy!

    $ cd /krypton/krypton1
    $ ls
    README  krypton2
    $ cat krypton2
    YRIRY GJB CNFFJBEQ EBGGRA
    
## Solutions ##

### Python ###

print "YRIRY GJB CNFFJBEQ EBGGRA".decode(encoding="ROT13")

### Hand Decode ###

Write down the alphabet in normal order A, B, C, etc.

Then write down the alphabet in the opposite direction underneath.  I start at Z and work backwards with A, B, C, etc.

You now have rotated the 26-characters around its midpoint and can decode the message with this key.

## Theory ##

The theory is in the "Hand Decode" section.  Note that it is normal to obfuscate the cipher text further by grouping into 5 letter clusters.

# Level 2 #

[overthewire.org/wargames/krypton/krypton2.html](http://overthewire.org/wargames/krypton/krypton2.html)

Use the password to log into the second level.

    ssh krypton2@krypton.labs.overthewire.org

## The Challenge ##

> Substitution ciphers are a simple replacement algorithm. In this example of a substitution cipher, we will explore a ‘monoalphebetic’ cipher. Monoalphebetic means, literally, “one alphabet” and you will see why.

> This level contains an old form of cipher called a ‘Caesar Cipher’. A Caesar cipher shifts the alphabet by a set number. For example:

    plain:  a b c d e f g h i j k ... 
    cipher: G H I J K L M N O P Q ...

> In this example, the letter ‘a’ in plaintext is replaced by a ‘G’ in the ciphertext so, for example, the plaintext ‘bad’ becomes ‘HGJ’ in ciphertext.

> The password for level 3 is in the file krypton3. It is in 5 letter group ciphertext. It is encrypted with a Caesar Cipher. Without any further information, this cipher text may be difficult to break. You do not have direct access to the key, however you do have access to a program that will encrypt anything you wish to give it using the key. If you think logically, this is completely easy.

> One shot can solve it!

This challenge uses the alphabet shifted by a fixed number of characters.  These are called
    
    $ cd /krypton/krypton2
    $ ls
    README  encrypt  keyfile.dat  krypton3
    $ cat krypton3 
    OMQEMDUEQMEK

## Solutions ##

### Python ###

You can read the python solution on [nsimattstiles.wordpress.com/2014/04/30/overthewire-krypton-level-0-5-writeup/](http://nsimattstiles.wordpress.com/2014/04/30/overthewire-krypton-level-0-5-writeup/). 
I think he over-complicated this quite a bit.  

### My Method ###

A common technique is to put known plaintext into the encryption process to see what you get out.  All we need is to know where A goes.  
The challenge I had here was creating an environment where encrypt could run and read my input file.   

    $ ./encrypt 
    usage: encrypt foo  - where foo is the file containing the plaintext
    $ touch plain.txt
    touch: cannot touch 'plain.txt': Permission denied
    $ mkdir /tmp/kryptJeff   # Make a folder I can create files in
    $ cd !$
    $ chmod ugo+rwx . 
    $ cp /krypton/krypton2/* .
    cp: cannot open '/krypton/krypton2/keyfile.dat' for reading: Permission denied
    $ touch keyfile.dat
    $ cat > plain.txt 
    ABCDEFG
    # Ctrl-D to end text file entry
    $ cat plain.txt 
    ABCDEFG
    $ ./encrypt plain.txt
    $ cat ciphertext 
    MNOPQRS

(This worked because keyfile.dat is extraneous but required for encrypt to run.)

So 'A' maps to 'M'.  Now we can build a key.
Built in Google Docs spreadsheet a key.  Column with alphabet in it.  Then I copied the column starting with M down to Z and 
pasted it next to A.   I then decoded the next password.

## Theory ##

The theory behind this is that it only takes the discovery of the constant shift to break this code.  This is a common weakness against
bad encryption methods.  You are using the cipher against itself.

# Level 3 #

[overthewire.org/wargames/krypton/krypton3.html](http://overthewire.org/wargames/krypton/krypton3.html)

Use the password to log into the third level.

    ssh krypton3@krypton.labs.overthewire.org

## The Challenge ##

> The main weakness of a simple substitution cipher is repeated use of a simple key. In the previous exercise you were able to introduce arbitrary plaintext to expose the key. In this example, the cipher mechanism is not available to you, the attacker.

> However, you have been lucky. You have intercepted more than one message. The password to the next level is found in the file ‘krypton4’. You have also found 3 other files. (found1, found2, found3)

> You know the following important details:

> The message plaintexts are in English (*** very important) - They were produced from the same key (*** even better!)

