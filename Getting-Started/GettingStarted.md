- [Getting started](#getting-started)
  - [Description](#description)
  - [Files](#files)
- [Instructions](#instructions)
  - [Running `gs`](#running-gs)
  - [Modfying `wrapper.py`](#modfying-wrapperpy)
- [Flag](#flag)
  - [Issues encountered](#issues-encountered)
- [Lessons learned](#lessons-learned)
- [References](#references)

# Getting started
## Description

> Get ready for the last guided challenge and your first real exploit. It’s time to show your hacking skills.

We are given a `.zip` containing several files. We are also given a docker container with an IP address.

## Files

[GettingStarted.zip](/Getting-Started/GettingStarted.zip)

# Instructions

Let us examine the contents of the zip.

`unzip GettingStarted.zip`

`cd /GettingStarted/challenge`

`file *`

> **flag.txt**:   ASCII text
> 
> **glibc**:      directory
> 
> **gs**:         ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter ./glibc/ld-linux-x86-64.so.2, BuildID[sha1]=505eb225ba13a677aa5f00d5e3d840f63237871f, for GNU/Linux 3.2.0, not stripped
> 
> **wrapper.py**: Python script, ASCII text executable

`flag.txt` file contains a test flag; this is **not** the flag needed to solve the challenge.

`glibc` is a directory contains some libc files that help the `gs` binary run.

`gs` is a 64-bit ELF executable that is [not stripped](https://unix.stackexchange.com/a/2972). We will come back to this file in a minute.

`wrapper.py` file is a python script. This will help later, with the docker container.

## Running `gs`

Running gs `./gs` will show a [stack frame layout](https://en.wikipedia.org/wiki/Call_stack). This layout includes labels for various parts of the buffer, including the address value that we are meant to override `0x00000000deadbeef`

We are instructed to fill and overwrite the 32-byte buffer of `gs` and are given an example of using the characters A and B.

There are a few ways we could approach filling the buffer. The simpliest is to use the prompt provided `>>` in gs, and enter varrying amount of A's, B's, etc. through trial and error, opening the file each time it isn't long enough.

A faster alternative is to use a command when launching the file (credit: [siunam321](https://siunam321.github.io/ctf/Cyber-Apocalypse-2023/Pwn/Getting-Started/))

`python3 -c "print('A' * 32 + 'B' * 8)" | ./gs`

After performing the [stack buffer overflow](https://en.wikipedia.org/wiki/Stack_buffer_overflow) against `gs` you will see the test flag `HTB{f4k3_fl4g_4_t35t1ng}`. The test flag serves as an indicator that our stack buffer overflow is long enough.

With this new information, it is now time to modify `wrapper.py`

## Modfying `wrapper.py`

After opening `wrapper.py` in your text editor, you will see instructions to [install pwntools](https://docs.pwntools.com/en/stable/install.html), if you have not done so already. You will also want to make sure that you are using `python3` to run wrapper.

* On line 12 change `0.0.0.0` to the docker IP address
* On line 13 change `1337` to the docker port
* On line 18 change the code
  * From `payload = b'A' * 10`
  * To `payload = b'A' * 32 + b'B' * 8`

When completed, run `python3 ./wrapper.py`

# Flag

After modifying `wrapper.py` and running it you will receive flag `HTB{b0f_s33m5_3z_r1ght?}`

## Issues encountered

It is possible to see the test flag `HTB{f4k3_fl4g_4_t35t1ng}` and *also* see "You failed!" if the stack buffer overflow used is over 40 bytes.

Technically only the "correct" amount of 32 A's and 8 B's *should* be used, but you could use 41 or more, and still get confirmation with the test flag in `gs`.

# Lessons learned

Basics of Stack Buffer Overflow 

# References

In no particular order

* https://fazect.github.io/htb2023/#getting-started
* https://github.com/An00bRektn/CTF/tree/main/live_events/htb_cyber_apocalypse_23/pwn/pwn_getting_started
* https://medium.com/@damaidec/cyber-apocalypse-2023-writeup-308ef9a94828
* https://4trxia.github.io/posts/Hackthebox-ctf-2023/#getting-started-pwn-very-easy
* https://github.com/Mymaqn/HTBCA2023_Pwn_Writeups/tree/master/getting_started
* https://ilter.tech/blog/htb-pwn-getting-started/
* https://github.com/D13David/ctf-writeups/tree/main/cyber_apocalypse23/pwn/getting_started
* https://gitlab.com/Cryptopone/cyberapocalypse2023-writeups/-/tree/main/Pwn/Getting%20Started
* https://github.com/LazyTitan33/CTF-Writeups/blob/main/HTB%20-%20CyberApocalypse_2023/Pwn/Getting_Started.md
* https://github.com/Fra-SM/CyberApocalypse2023_CTF_Writeups/blob/main/Pwn/Getting%20started.md
* https://docs404.com/hack-the-box-ca-2023/pwn-getting-started/
* https://github.com/sbencoding/htb_ca2023_writeups/tree/master/pwn/getting_started
* https://siunam321.github.io/ctf/Cyber-Apocalypse-2023/Pwn/Getting-Started/
* https://twc1rcle.com/ctf/team/ctf_writeups/cyber_apocalypse_2023/#getting-started