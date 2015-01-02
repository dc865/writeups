
Documenting a walkthrough of the [Behemoth](http://overthewire.org/wargames/behemoth/) web challenge on OverTheWire.org.  

Description from website: 

> This wargame deals with a lot of regular vulnerabilities found commonly 'out 
> in the wild'. While the game makes no attempts at emulating a real environment
> it will teach you how to exploit several of the most common coding mistakes 
> including buffer overflows, race conditions and privilege escalation.

Written following the Markdown style guide at [daringfireball.net/projects/markdown/syntax](http://daringfireball.net/projects/markdown/syntax)

# Level 0 #

    ssh behemoth0@behemoth.labs.overthewire.org
    
Password is behemoth0

## The Challenge ##
    
Get the Level 1 password in file /etc/behemoth_pass/behemoth1.

## Solution ##

### Bash/Unix Terminal ###

    cd /behemoth
    behemoth0@melinda:/behemoth$ ls
    behemoth0  behemoth1  behemoth2  behemoth3  behemoth4  behemoth5  behemoth6  behemoth6_reader  behemoth7
    
These are displayed in red indicating setuid.

    behemoth0@melinda:/behemoth$ ls -al 
	total 65
	drwxr-xr-x  2 root      root      1024 Nov 14 10:32 .
	drwxr-xr-x 12 root      root      1024 Nov 14 10:28 ..
	-r-sr-x---  1 behemoth1 behemoth0 5929 Nov 14 10:32 behemoth0
	-r-sr-x---  1 behemoth2 behemoth1 5114 Nov 14 10:32 behemoth1
	-r-sr-x---  1 behemoth3 behemoth2 7584 Nov 14 10:32 behemoth2
	-r-sr-x---  1 behemoth4 behemoth3 5224 Nov 14 10:32 behemoth3
	-r-sr-x---  1 behemoth5 behemoth4 7609 Nov 14 10:32 behemoth4
	-r-sr-x---  1 behemoth6 behemoth5 7903 Nov 14 10:32 behemoth5
	-r-sr-x---  1 behemoth7 behemoth6 7557 Nov 14 10:32 behemoth6
	-r-xr-x---  1 behemoth7 behemoth6 7601 Nov 14 10:32 behemoth6_reader
	-r-sr-x---  1 behemoth8 behemoth7 5800 Nov 14 10:32 behemoth7

Only one we can run is behemoth0.  Running it and trying the behemoth0 password...

	behemoth0@melinda:/behemoth$ ./behemoth0 
	Password: behemoth0
	Access denied..

Try running strings to see if password is in the clear...

	behemoth0@melinda:/behemoth$ strings behemoth0 
	/lib/ld-linux.so.2
	libc.so.6
	_IO_stdin_used
	execl
	memfrob
	__isoc99_scanf
	puts
	__stack_chk_fail
	printf
	strlen
	strcmp
	__libc_start_main
	__gmon_start__
	GLIBC_2.7
	GLIBC_2.4
	GLIBC_2.0
	PTRh
	D$l1
	OK^G
	D$#SYBE
	D$'X^Y
	T$le3
	[^_]
	unixisbetterthanwindows
	followthewhiterabbit
	pacmanishighoncrack
	Password: 
	%64s
	Access granted..
	/bin/sh
	Access denied..
	;*2$"

Tried the three obvious strings: unixisbetterthanwindows, followthewhiterabbit, and pacmanishighoncrack.  None worked.

Comparing writeups at [http://hacktracking.blogspot.com/2013/03/behemoth-wargame-level-0.html] and 
[https://github.com/m4tux/CTF/blob/master/exploitation/overthewire/Behemoth/behemoth0] they both have a method that uses
_gdb_ to debug the application.  But I also found that their breakpoints are not correct for the version on OverTheWire.

The second writeup has a nice, simple _ltrace_ solution to the challenge.  You can follow it and quickly solve the challenge.

Here I am recording an expanded version of the _gdb_ solution.  Partially followed the examples at 
[http://0xdeadbeef.us/index.php?/archives/7-Fun-with-overflows.html] on using _gdb_.  

The problem is that for the two writeups they both start with "break *0x080485db", but this address is incorrect and its
determination is not described.  I tried figuring out the address by hexdumping the binary, but couldn't figure out 
the origin of the address.

I used the disassemble main command from 0xdeadbeef.us to figure it out.

	behemoth0@melinda:/behemoth$ gdb -q /behemoth/behemoth0
	Reading symbols from /behemoth/behemoth0...(no debugging symbols found)...done.
	(gdb) disassemble main
	Dump of assembler code for function main:
	   0x080485a2 <+0>:	push   %ebp
	   0x080485a3 <+1>:	mov    %esp,%ebp
	   0x080485a5 <+3>:	and    $0xfffffff0,%esp
	   0x080485a8 <+6>:	sub    $0x70,%esp
	   0x080485ab <+9>:	mov    %gs:0x14,%eax
	   0x080485b1 <+15>:	mov    %eax,0x6c(%esp)
	   0x080485b5 <+19>:	xor    %eax,%eax
	   0x080485b7 <+21>:	movl   $0x475e4b4f,0x1f(%esp)
	   0x080485bf <+29>:	movl   $0x45425953,0x23(%esp)
	   0x080485c7 <+37>:	movl   $0x595e58,0x27(%esp)
	   0x080485cf <+45>:	movl   $0x8048720,0x10(%esp)
	   0x080485d7 <+53>:	movl   $0x8048738,0x14(%esp)
	   0x080485df <+61>:	movl   $0x804874d,0x18(%esp)
	   0x080485e7 <+69>:	movl   $0x8048761,(%esp)
	   0x080485ee <+76>:	call   0x8048400 <printf@plt>
	   0x080485f3 <+81>:	lea    0x2b(%esp),%eax
	   0x080485f7 <+85>:	mov    %eax,0x4(%esp)
	   0x080485fb <+89>:	movl   $0x804876c,(%esp)
	   0x08048602 <+96>:	call   0x8048470 <__isoc99_scanf@plt>
	   0x08048607 <+101>:	lea    0x1f(%esp),%eax
	   0x0804860b <+105>:	mov    %eax,(%esp)
	   0x0804860e <+108>:	call   0x8048440 <strlen@plt>
	   0x08048613 <+113>:	mov    %eax,0x4(%esp)
	   0x08048617 <+117>:	lea    0x1f(%esp),%eax
	   0x0804861b <+121>:	mov    %eax,(%esp)
	   0x0804861e <+124>:	call   0x804857d <memfrob>
	   0x08048623 <+129>:	lea    0x1f(%esp),%eax
	   0x08048627 <+133>:	mov    %eax,0x4(%esp)
	   0x0804862b <+137>:	lea    0x2b(%esp),%eax
	   0x0804862f <+141>:	mov    %eax,(%esp)
	   0x08048632 <+144>:	call   0x80483f0 <strcmp@plt>
	   0x08048637 <+149>:	test   %eax,%eax
	   0x08048639 <+151>:	jne    0x8048665 <main+195>
	   0x0804863b <+153>:	movl   $0x8048771,(%esp)
	   0x08048642 <+160>:	call   0x8048420 <puts@plt>
	   0x08048647 <+165>:	movl   $0x0,0x8(%esp)
	   0x0804864f <+173>:	movl   $0x8048782,0x4(%esp)
	   0x08048657 <+181>:	movl   $0x8048785,(%esp)
	   0x0804865e <+188>:	call   0x8048460 <execl@plt>
	   0x08048663 <+193>:	jmp    0x8048671 <main+207>
	   0x08048665 <+195>:	movl   $0x804878d,(%esp)
	   0x0804866c <+202>:	call   0x8048420 <puts@plt>
	   0x08048671 <+207>:	mov    $0x0,%eax
	   0x08048676 <+212>:	mov    0x6c(%esp),%edx
	   0x0804867a <+216>:	xor    %gs:0x14,%edx
	   0x08048681 <+223>:	je     0x8048688 <main+230>
	   0x08048683 <+225>:	call   0x8048410 <__stack_chk_fail@plt>
	   0x08048688 <+230>:	leave  
	   0x08048689 <+231>:	ret    
	End of assembler dump.

Look for the address where strcmp is called; it is 0x08048632.  The registers will be loaded by this point in preparation
for the string comparison.  The remainder follows as in the writeups.

	(gdb) break *0x08048632
	Breakpoint 1 at 0x8048632
	(gdb) run
	Starting program: /games/behemoth/behemoth0 
	Password: AAA

	Breakpoint 1, 0x08048632 in main ()
	(gdb) x/xw $esp
	0xffffd640:	0xffffd66b
	(gdb) x/s 0xffffd66b
	0xffffd66b:	"AAA"
	(gdb) x/xw $esp+0x4
	0xffffd644:	0xffffd65f
	(gdb) x/s 0xffffd65f
	0xffffd65f:	"eatmyshorts"
	
	quit 
	
Finally, finishing the level.

	behemoth0@melinda:/behemoth$ ./behemoth0 
	Password: eatmyshorts
	Access granted..
	$ whoami                     
	behemoth1
	$ cat /etc/behemoth_pass/behemoth1
	[PASSWORD DELETED]
	exit
	exit

# Level 1 #

    ssh behemoth1@behemoth.labs.overthewire.org
    
Password is obtained from the previous level.

## The Challenge ##
    
Get the Level 2 password in file /etc/behemoth_pass/behemoth2.

## Solution ##

### Bash/Unix Terminal ###

Initial attempt to see if the _ltrace_ solution will work.

	behemoth1@melinda:~$ whoami 
	behemoth1
	behemoth1@melinda:~$ cd /behemoth
	behemoth1@melinda:/behemoth$ ./behemoth1 
	Password: 
	Authentication failure.
	Sorry.
	behemoth1@melinda:/behemoth$ ltrace ./behemoth1 
	__libc_start_main(0x804845d, 1, 0xffffd784, 0x80484a0 <unfinished ...>
	printf("Password: ")                                                     = 10
	gets(0xffffd69d, 0, 194, 0xf7eb6de6Password: AAA
	)                                     = 0xffffd69d
	puts("Authentication failure.\nSorry."Authentication failure.
	Sorry.
	)                                  = 31
	+++ exited (status 0) +++

