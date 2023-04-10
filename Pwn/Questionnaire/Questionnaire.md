- [Questionnaire](#questionnaire)
  - [Description](#description)
  - [Files](#files)
- [Instructions](#instructions)
  - [Examining `test.c`](#examining-testc)
  - [Examining `test`](#examining-test)
  - [Moving on](#moving-on)
    - [Questions 1-3](#questions-1-3)
    - [Question 4](#question-4)
      - [Method 1 - gdb](#method-1---gdb)
      - [Method 2 - checksec](#method-2---checksec)
      - [Method 3 - readelf](#method-3---readelf)
    - [Questions 5-8](#questions-5-8)
    - [Question 9](#question-9)
      - [Method 1 - Manual](#method-1---manual)
      - [Method 2 - python](#method-2---python)
      - [Method 3 - gdb](#method-3---gdb)
    - [Question 10](#question-10)
      - [Method 1 - Ghidra](#method-1---ghidra)
      - [Method 2 - gdb](#method-2---gdb)
      - [Method 3 - objdump](#method-3---objdump)
      - [Method 4 - readelf](#method-4---readelf)
- [Flag](#flag)
  - [Issues encountered](#issues-encountered)
- [Lessons Learned](#lessons-learned)
- [References](#references)
  - [Additional resources](#additional-resources)


# Questionnaire
## Description

> It's time to learn some things about binaries and basic c. Connect to a remote server and answer some questions to get the flag.

We're given a docker container to spawn, and a `.zip` to download.

The `.zip` contains two files:
* `test.c`
* `test`

## Files

[Questionnaire.zip](/Pwn/Questionnaire/Questionnaire.zip)

# Instructions

After running `unzip Questionnaire.zip` we can examine these files using `file`

```
$ file *
test:   ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=5a83587fbda6ad7b1aeee2d59f027a882bf2a429, for GNU/Linux 3.2.0, not stripped
test.c: C source, ASCII text
```

## Examining `test.c`

This file shows a comment:

```
This is not the challenge, just a template to answer the questions.
To get the flag, answer the questions. 
here is no bug in the questionnaire.
```

## Examining `test`

This file is a compiled C file. 

## Moving on

Next, `nc` to the IP address and port provided by the docker container.

`nc 123.456.789.012 1337`

The `nc` connection will provides a number of tips about how to examine file information, followed by a prompt at the bottom where we will be asked a series of questions about `test`.

### Questions 1-3

Questions 1-3 can be answered with the `file` information gathered above.

### Question 4

The protections enabled on `test` can be found using a few different methods:

#### Method 1 - gdb

The first method we can use is a powerful program called [gdb](https://en.wikipedia.org/wiki/GNU_Debugger) (GNU Debugger).

Note: `gdb` can do much more than just check for basic protections. This is doubly true if you use the gdb plugin [gef](https://github.com/hugsy/gef/blob/dev/README.md)!

`gdb test` -> `checksec`

#### Method 2 - checksec

The second method we can use is through a tool called [pwntools](https://docs.pwntools.com/en/stable/commandline.html). More about pwntools can be found [here](https://docs.pwntools.com/en/stable/).

`checksec test`

OR

`checksec --file=test`

The only notable difference between each method is wether the information should be displayed in a "top-to-bottom" layout, or a "left-to-right" layout.

#### Method 3 - readelf

[One writeup](https://docs404.com/hack-the-box-ca-2023/pwn-questionaire/) suggests using `readelf` to determine file protections. 

`readelf -a test`

Note: From a noobie perspective, it's hard to tell what exactly is being done to determine how file permissions are being checked using this method.

### Questions 5-8

These both can be answered by examining `test.c`

### Question 9

Question 9 asks how many bytes of input have to be input for a segmentation fault for to occur. There are a few methods that can be performed to solve this.

#### Method 1 - Manual

The simplest, but most time consuming, is trial and error.

`gdb test` -> `run` -> AAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
`gdb test` -> `run` -> AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
`gdb test` -> `run` -> AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

#### Method 2 - python

A slightly faster method is with some basic python.

`python3 -c "print('A' * 30)" | ./test`
`python3 -c "print('A' * 39)" | ./test`
`python3 -c "print('A' * 40)" | ./test`

#### Method 3 - gdb

If you used `gdb` for the previous method from Question 4 this option probably makes the most sense to go with. More information of this method can be found [here](https://youtu.be/Mz4ifmzsQiM?list=PLUrAjkLbvTpKBgf4uRsc2JWhs8OZeswah&t=174).

* `pattern create 100`
* `r <<< $(echo 'AAAAAGQBFAAAAAAAAAAAAAAAAADAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAALFEACAABDAAAAAAAAAAAAAA')`
* `pattern search "A)AAEAAa"`
* The segmentation fault occurs at RSP -> Offset 40

### Question 10

Question 10 is probably the hardest of all of the questions asked in this challenge. The information we need to provide is the address of function `gg`. Luckily, we have several ways to find the answer to this:

#### Method 1 - Ghidra

The (long, slow, and stupid) method that I personally used was Ghidra.

BLAH BLAH BLAH WRITE INFO HERE.

#### Method 2 - gdb

A far easier solution is to use trusty `gdb`!

`p gg`

Will return something like:

> $1 = {<text variable, no debug info>} **0x401176** \<gg>

#### Method 3 - objdump

`objdump -D -F test | grep gg`

This will return:

> **0000000000401176** \<gg> (File Offset: 0x1176):

Which can then be shortened to 0x401176.

#### Method 4 - readelf

An interesting alternative posted [here](https://4trxia.github.io/posts/Hackthebox-ctf-2023/#questionnaire-pwn-very-easy) suggests using `readelf`.

`readelf -a test`

I've modified the command slightly to make it a bit easier to find:

`readelf -a test | grep gg`

Which returns the following

> 25: **0000000000401176**    26 FUNC    GLOBAL DEFAULT   15 gg

Which can then be shortened to 0x401176.

# Flag

HTB{th30ry_bef0r3_4cti0n}

## Issues encountered

For the question

> What is the address of gg() in hex? (e.g. 0x401337)

I struggled to find this in `Ghidra`. I hadn't used Ghidra before, so I tried to use ChatGPT help instruct on where to find the information requested. ChatGPT made things **worse**, because it was making up functions and options that do not exist! I strongly discourage the use of ChatGPT for this purpose

By comparison, the YouTuber did a pretty simple solution, without using Ghidra at all:
* `gdb`
* `disass gg`
* The top result, minus the leading 0's
  * `0x401176`

# Lessons Learned

1. Do not use ChatGPT to write instructions of how to use software.
2. I need to get familiar with `gdb`.
3. I need to install `gef`.
4. I should try and read up on `readelf`. It seems to be niche, but could provide some real value.
5. 

# References

In no particular order

* https://fazect.github.io/htb2023/#questionnaire
* https://4trxia.github.io/posts/Hackthebox-ctf-2023/#questionnaire-pwn-very-easy
* https://github.com/Mymaqn/HTBCA2023_Pwn_Writeups/tree/master/questionnaire
* https://ilter.tech/blog/htb-pwn-questionnaire/
* https://github.com/D13David/ctf-writeups/tree/main/cyber_apocalypse23/pwn/questionnaire
* https://gitlab.com/Cryptopone/cyberapocalypse2023-writeups/-/tree/main/Pwn/Questionnaire
* https://github.com/LazyTitan33/CTF-Writeups/blob/main/HTB%20-%20CyberApocalypse_2023/Pwn/Questionnaire.md
* https://www.youtube.com/watch?v=Mz4ifmzsQiM&list=PLUrAjkLbvTpKBgf4uRsc2JWhs8OZeswah&index=12
* https://github.com/Fra-SM/CyberApocalypse2023_CTF_Writeups/blob/main/Pwn/Questionnaire.md
* https://docs404.com/hack-the-box-ca-2023/pwn-questionaire/
* https://github.com/sbencoding/htb_ca2023_writeups/blob/master/pwn/questionnaire.md
* https://siunam321.github.io/ctf/Cyber-Apocalypse-2023/Pwn/Questionnaire/
* https://twc1rcle.com/ctf/team/ctf_writeups/cyber_apocalypse_2023/#questionnaire
* https://medium.com/@damaidec/cyber-apocalypse-2023-writeup-308ef9a94828
  * Section is about half way down


## Additional resources

* Info about `pwntools`

https://docs.pwntools.com/en/stable/

* How to install `gef`

https://hugsy.github.io/gef/#quick-start

* How to use `gef`

This isn't a particularly great presentation, but serves as a reasonable introduction to `gef`, which I have no previous familiarity with.

https://www.youtube.com/watch?v=KWG7prhH-ks&list=PLjAuO31Rg972WeMvdR_57Qu-aVM8T6DkQ

