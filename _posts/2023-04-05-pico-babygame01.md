---
title: PicoCTF 2023 - Babygame01
date: 2023-04-05 1:00:00 +0200
categories: [PicoCTF2023]
tags: [Pwn]
---

Babygame01 gave us a game, that we could both run locally and with an instance, that we had to exploit, we got a terminal based game, where we had to move to a place with a character that was a "@", and then we won, but we also had to get the flag, which is the hard part

![Image of the game](https://lh3.googleusercontent.com/mfr1SA4vGzsS_cwgmxxfOxdccQc29nfJojpY-AvYOmTg98CVae6zWgmtfIQL6DjEjaxNIQHQS4A21fhbakaKwujzlatNRR7G5OVJ3ljjzxAuUmnUYQaPuambsVCEYu3Z2Er6rvZL)

This is how the game looks, we can move by typing in **w a s** or **d**. The challenge is, that we somehow have to set the flag variable, that we can see at the top saying: `Player has flag: 0` to something, maybe 1? Well, we got the program, let's look at it in Ghidra!

The challenge has some hidden commands, that make our lives easier, that we can find here, in the move function:

![Image of Ghidra decompilation](https://lh3.googleusercontent.com/wd5vrntsaRjC4gXO4XiEZL_tWp658TIMjmM7a4XPbOu82ApUhztJc3PIpuW0PllLeDzEuIzJ7Q6u28waaePhM4wbCZ0HGlOnSJJHAVfx6Ak7oX9PvmDPkXNX99OsyjeblEz2sliJ)

We can see that we have **wasd** to move, but we also have two other letters, **p** and **l**. **l** just changes our character, @ to something else, that's not really important now. But if we type **p**, it calls solve round, and jumps to the finish line instantly, this might be useful if we don't wanna go back with our flag manually.
```c
if (param_2 == 'p') {
    solve_round(param_3,param_1);
}
```

Now let's look at the check for the flag variable.

We can see in the main function, that there is some code that checks if a variable is 0, and if it's not, it calls the win function:
```c
if (local_aa4 != '\0') {
    puts("flag");
    win();
    fflush(stdout);
}
```

It only calls this function though, if we already are in the finish, otherwise the execution does not reach this code, meaning we have to change the variable then get into the finish.


Let's look at the win function:
```c
  local_10 = *(int *)(in_GS_OFFSET + 0x14);
  __stream = fopen("flag.txt","r");
  if (__stream == (FILE *)0x0) {
    puts("flag.txt not found in current directory");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  fgets(local_4c,0x3c,__stream);
  printf(local_4c);
```

we can see that it basically just opens the file flag.txt and prints it out for us.

Now that we know all this, how do we change the variable? If we look at the code, the variable is just initialized as 0, and is changed nowhere, meaning we can't change it a regular way, we need an __overflow__.

Let's try to spam the letter a for example and see what happens. Let's try with for example, spamming 442 letter "a". This number is random, you can make it any size and see what happens.

![Image of the game crash](https://lh3.googleusercontent.com/ZEDW7TR6AiGw05PVdjBSeYCzx99YZNiPt1_bFrvOqoet2xZL3GSqPJNtR-rOP6MFXeQNNjcwbF6nueYr8P_V8VaZnR2tj310jYQdM-zhw-VlRDVKuYEWGEHmSQUUpM4Yl3oPuSah)

First, we can obviously see that the program crashed, by the 
```
[1]    50258 segmentation fault (core dumped)  ./game
```

But hey, what is that at the top? `Player has flag: 46`, we can  remember it used to be `0`, so, what happened? We overflowed a buffer, and overwrote the value of that variable, and since the value we are looking for is any value not zero, this is good for us. 

Only one problem, the program crashed. We can try making it not crash by using less of the letter "a", but still enough for it to overwrite the program. This is a case of trial and error, but in the end it turns out we need exactly __369__ "a" to overflow the integer but not crash the program, so let's try that.

Running it we can see the program still running, and the value changed, now remember that we can go to the finish line by typing `p`? Let's try that!

![Game finished and flag printed](https://lh3.googleusercontent.com/vk68WerijoWoD_LpDAZGprfCtenwQrrOyLQj90cvQY8E1AUKErTXx3Fr2-PxGYFMbdJAz5P7LPW2t2G_swQhHERp6f1b2QubsRU4QscTb4GUXuKdlTOMGEO4EJGbcNw7rLAmbZeW)
