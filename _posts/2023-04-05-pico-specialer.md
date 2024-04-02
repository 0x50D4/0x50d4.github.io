---
title: PicoCTF 2023 - Specialer
date: 2023-04-05 1:00:00 +0200
categories: [PicoCTF2023]
tags: [Linux]
---

In specialer, we get given an instance that we have to ssh into, so let's do that:
```bash
ssh -p <port> ctf-player@saturn.picoctf.net 
```
and use the given password.

We get a command line where we can try typing commands, so let's try a few things:
```
Specialer$ ls
-bash: ls: command not found
Specialer$ /bin/bash
Specialer$ ../../../../bin/bash
Specialer$ /bin/bsdlkfjsldkjflksdjf
bash: /bin/bsdlkfjsldkjflksdjf: No such file or directory
Specialer$ /bin/ls
bash: /bin/ls: No such file or directory
Specialer$ /bin/bash
Specialer$ /bin/sh
bash: /bin/sh: No such file or directory
```

I also tried other commands but non seem to really work, we only seem to have bash on this system. How do we find the flag then?

We can check [this site](https://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html) to see the built in commands in bash, and we can see, that amongst other things, we have access to echo, but what can we use echo for?

First we have to find the file the flag is in, so let's try to do something like the command `ls` just with `echo`.

Well, it turns out if we type `echo *` it lists all the folders in the current directory, since the asterisk returns all matching file names, and since * means everything, it returns everything, then echo just outputs it to the command line. So let's try that:

```
Specialer$ echo *
abra ala sim
```
Great! We can see that we have 3 directories, __abra__, __ala__ and __sim__, let's try looking inside of those too, we can do this by typing the folder name, then a slash with an asterisk after it like so: `echo folder/*`. So let's try it:
```
Specialer$ echo abra/*
abra/cadabra.txt abra/cadaniel.txt
Specialer$ echo ala/*
ala/kazam.txt ala/mode.txt
Specialer$ echo sim/*
sim/city.txt sim/salabim.txt
```
We can probably guess that the flag is in one of these files, but we have to somehow print out the contents of the file first.
Based on [this stackexchange post](https://unix.stackexchange.com/questions/63658/redirecting-the-content-of-a-file-to-the-command-echo/363674#363674), we can just use `echo "$(<file.txt)"` to print out the contents of a file. So let's see if it works:

```
Specialer$ echo "$(<ala/mode.txt)"
Yummy! Ice cream!
Specialer$ echo "$(<ala/kazam.txt )"
return 0 picoCTF{ find the flag yourself ;) }
```
aaand we got the flag. I was lucky choosing the right folder first. But why does `echo "$(<file.txt)"` work? We know what echo does, with `"$()"` we tell it to perform a command, and it gives back the output as if the user typed in a the text, and with `<` we just perform input redirection, similar to when you for example push the output of a command to a file, just in reverse.

