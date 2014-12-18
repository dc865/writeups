Oops, I named the file incorrectly.  It is Krypton, not Kryptos.

Documenting a walkthrough of the [Krypton](http://overthewire.org/wargames/krypton/) web challenge on OverTheWire.org.  

This is mostly following the writeup by Matt Stiles [nsimattstiles.wordpress.com/2014/04/30/overthewire-krypton-level-0-5-writeup/](http://nsimattstiles.wordpress.com/2014/04/30/overthewire-krypton-level-0-5-writeup/).  All of his solutions are shown in python.

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

The next level's README contains this comment:

> Hopefully you just encrypted the alphabet a plaintext to fully expose the key in one swoop.

Well, we kind of did this.

# Level 3 #

[overthewire.org/wargames/krypton/krypton3.html](http://overthewire.org/wargames/krypton/krypton3.html)

Use the password to log into the third level.

    ssh krypton3@krypton.labs.overthewire.org

## The Challenge ##

> The main weakness of a simple substitution cipher is repeated use of a simple key. In the previous exercise you were able to introduce arbitrary plaintext to expose the key. In this example, the cipher mechanism is not available to you, the attacker.

> However, you have been lucky. You have intercepted more than one message. The password to the next level is found in the file ‘krypton4’. You have also found 3 other files. (found1, found2, found3)

> You know the following important details:

> The message plaintexts are in English (*** very important) - They were produced from the same key (*** even better!)

    $ cd /krypton/krypton3
    $ ls
    HINT1  HINT2  README  found1  found2  found3  krypton4
    $ cat HINT1 
    Some letters are more prevalent in English than others.
    $ cat HINT2
    "Frequency Analysis" is your friend.
    $ cat krypton4 
    KSVVW BGSJD SVSIS VXBMN YQUUK BNWCU ANMJS
    
The found* files are too long to list.
    
## Solutions ##

Read the solution written up by Matt Stiles.  It is well-done.  My only comment is that I misinterpreted his key at the end.  
Use it to step through the ciphertext from Alphabet to Encryption.  Also, he did not put the letter for 'j' in the table correctly.  
I had to read his notes to get the correct translation for 'J'.

I don't have any better method to go after these types of puzzles.  Does anybody else have a favorite method?

## Theory ##

The fact that letters occur in a language with varying frequency gives rise to the technique of "frequency-analysis" which is applied to the ciphertext.  
Frequency-analysis is a weakness for any simple substitution cipher.  

# Level 4 #

[overthewire.org/wargames/krypton/krypton4.html](http://overthewire.org/wargames/krypton/krypton4.html)

Use the password to log into the fourth level.

    ssh krypton4@krypton.labs.overthewire.org

## The Challenge ##

> So far we have worked with simple substitution ciphers.  They have
also been 'monoalphabetic', meaning using a fixed key, and 
giving a one to one mapping of plaintext (P) to ciphertext (C).
Another type of substitution cipher is referred to as 'polyalphabetic',
where one character of P may map to many, or all, possible ciphertext 
characters.

> An example of a polyalphabetic cipher is called a **Vigenere Cipher**.  It works
like this:

> If we use the key(K)  'GOLD', and P = PROCEED MEETING AS AGREED, then "add"
P to K, we get C.  When adding, if we exceed 25, then we roll to 0 (modulo 26).

> P     P R O C E   E D M E E   T I N G A   S A G R E   E D
> K     G O L D G   O L D G O   L D G O L   D G O L D   G O

> becomes:

> P     15 17 14 2  4  4  3 12  4 4  19  8 13 6  0  18 0  6 17 4 4   3
> K     6  14 11 3  6 14 11  3  6 14 11  3  6 14 11  3 6 14 11 3 6  14
> C     21 5  25 5 10 18 14 15 10 18  4 11 19 20 11 21 6 20  2 8 10 17

> So, we get a ciphertext of: VFZFK SOPKS ELTUL VGUCH KR

>This level is a Vigenere Cipher.  You have intercepted two longer, English 
language messages.  You also have a key piece of information.  You know the 
key length!

> For this exercise, the key length is 6.  The password to level five is in the usual
place, encrypted with the 6 letter key.

    $ cd /krypton/krypton4
    $ ls
    HINT  README  found1  found2  krypton5
    $ cat HINT 
    Frequency analysis will still work, but you need to analyse it
    by "keylength".  Analysis of cipher text at position 1, 6, 12, etc
    should reveal the 1st letter of the key, in this case.  Treat this as
    6 different mono-alphabetic ciphers...

    Persistence and some good guesses are the key!
    
    $ cat krypton5 
    HCIKV RJOX
    
## Solutions ##

Again, read the solution written up by Matt Stiles.  He used the website [smurfoncrack.com/pygenere/index.php](http://smurfoncrack.com/pygenere/index.php) to
analyze the text and return the plaintext and likely key.  Since we know the key length then breaks down quickly.  

I have a feeling there are some algorithms that can be applied since we have two encrypted texts, but did not come up with one.  

I used [www.cs.du.edu/~snarayan/crypt/vigenere.html](http://www.cs.du.edu/~snarayan/crypt/vigenere.html) with the ciphertext and key to decode the password.  I
first attempted it on paper, but did it backward.  I needed to subtract the key rather than add it.  I also needed to use the ordinal position of the letters in the alphabet; 
I was using their 0-based positions.

## Theory ##

I do not have any theory notes about these sorts of ciphers.  I wonder if they can be detected?  I'd guess that with a sufficiently long text and a short key, then repeating
combinations of letters would reveal the underlying structure.

Also note that the python source code is available from the SmurfOnCrack site.

# Level 5 #

[overthewire.org/wargames/krypton/krypton5.html](http://overthewire.org/wargames/krypton/krypton5.html)

Use the password to log into the fifth level.  Note that the password does not contain a space and is uppercase.

    ssh krypton5@krypton.labs.overthewire.org

## The Challenge ##

> Frequency analysis can break a known key length as well.  Lets try one
last polyalphabetic cipher, but this time the key length is unknown.

    $ cd /krypton/krypton5
    $ ls
    README  found1  found2  found3  krypton6
    $ cat krypton6 
    BELOS Z
    
## Solutions ##

I followed the same path as the previous Level.  The [smurfoncrack](http://smurfoncrack.com/pygenere/index.php) site is able to find the key as "KEYLENGTH."

# Level 6 #

[overthewire.org/wargames/krypton/krypton6.html](http://overthewire.org/wargames/krypton/krypton6.html)

Use the password to log into the sixth level.  Note that the password does not contain a space and is uppercase.  

    ssh krypton6@krypton.labs.overthewire.org

I've not completed this level.

## The Challenge ##

    $ cat README 
    Hopefully by now its obvious that encryption using repeating keys
    is a bad idea.  Frequency analysis can destroy repeating/fixed key
    substitution crypto.

    A feature of good crypto is random ciphertext.  A good cipher must
    not reveal any clues about the plaintext.  Since natural language 
    plaintext (in this case, English) contains patterns, it is left up
    to the encryption key or the encryption algorithm to add the 
    'randomness'.

    Modern ciphers are similar to older plain substitution 
    ciphers, but improve the 'random' nature of the key.

    An example of an older cipher using a complex, random, large key
    is a vigniere using a key of the same size of the plaintext.  For
    example, imagine you and your confident have agreed on a key using
    the book 'A Tale of Two Cities' as your key, in 256 byte blocks.

    The cipher works as such:

    Each plaintext message is broken into 256 byte blocks.  For each 
    block of plaintext, a corresponding 256 byte block from the book
    is used as the key, starting from the first chapter, and progressing.
    No part of the book is ever re-used as key.  The use of a key of the 
    same length as the plaintext, and only using it once is called a "One Time Pad".

    Look in the krypton6/onetime  directory.  You will find a file called 'plain1', a 256 
    byte block.  You will also see a file 'key1', the first 256 bytes of
    'A Tale of Two Cities'.  The file 'cipher1' is the cipher text of 
    plain1.  As you can see (and try) it is very difficult to break
    the cipher without the key knowledge.

    (NOTE - it is possible though.  Using plain language as a one time pad
    key has a weakness.  As a secondary challenge, open README in that directory)

    If the encryption is truly random letters, and only used once, then it
    is impossible to break.  A truly random "One Time Pad" key cannot be
    broken.  Consider intercepting a ciphertext message of 1000 bytes.  One
    could brute force for the key, but due to the random key nature, you would
    produce every single valid 1000 letter plaintext as well.  Who is to know
    which is the real plaintext?!?

    Choosing keys that are the same size as the plaintext is impractical.
    Therefore, other methods must be used to obscure ciphertext against 
    frequency analysis in a simple substitution cipher.  The
    impracticality of an 'infinite' key means that the randomness, or
    entropy, of the encryption is introduced via the method.

    We have seen the method of 'substitution'.  Even in modern crypto,
    substitution is a valid technique.  Another technique is 'transposition',
    or swapping of bytes.

    Modern ciphers break into two types; symmetric and asymmetric.

    Symmetric ciphers come in two flavours: block and stream.

    Until now, we have been playing with classical ciphers, approximating
    'block' ciphers.  A block cipher is done in fixed size blocks (suprise!).
    For example, in the previous paragraphs we discussed breaking text and keys
    into 256 byte blocks, and working on those blocks.  Block ciphers use a
    fixed key to perform substituion and transposition ciphers on each
    block discretely.

    Its time to employ a stream cipher.  A stream cipher attempts to create
    an on-the-fly 'random' keystream to encrypt the incoming plaintext one
    byte at a time.  Typically, the 'random' key byte is xor'd with the 
    plaintext to produce the ciphertext.  If the random keystream can be
    replicated at the recieving end, then a further xor will produce the
    plaintext once again.

    From this example forward, we will be working with bytes, not ASCII 
    text, so a hex editor/dumper like hexdump is a necessity.  Now is the
    right time to start to learn to use tools like cryptool.

    In this example, the keyfile is in your directory, however it is 
    not readable by you.  The binary 'encrypt6' is also available.
    It will read the keyfile and encrypt any message you desire, using
    the key AND a 'random' number.  You get to perform a 'known ciphertext'
    attack by introducing plaintext of your choice.  The challenge here is 
    not simple, but the 'random' number generator is weak.

    As stated, it is now that we suggest you begin to use public tools, like cryptool,
    to help in your analysis.  You will most likely need a hint to get going.
    See 'HINT1' if you need a kicktstart.

    If you have further difficulty, there is a hint in 'HINT2'.

    The password for level 7 (krypton7) is encrypted with 'encrypt6'.

    Good Luck!






