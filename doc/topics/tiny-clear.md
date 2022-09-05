# Tiny Clear Elf
> Tiny clear elf will be a collection of ELF executables in the nine CPU architectures that are currently supported on Debian GNU/Linux

## Why?
I have been frustrated by the way that different terminal emulators handle the `clear` command. It might not seem like a big deal, but it bothers me to no end - The two apps I run most frequently on my laptop are Firefox and the Kitty terminal emulator, so clearly I'm in the terminal a lot. I also run the `clear` command more-or-less constantly, often without thinking. While Kitty does many things I like, and its built-in image support allows me to do even more with it, I do prefer the classic `xterm` approach to handling `clear` - it clears the scrollback as well as the visible content. Kitty, on the other hand, leaves the scrollback untouched. A minor issue, to be `clear`, but an issue nonetheless. I've tested several other terminal emulators from the Debian Bullseye default repos, and they have different ways of handling it. 

This is the kind of silly, inconsequential thing that I can easily spend the better part of a year obsessing over.


## OBJECTIVES
I will make ELF executables that are as small as I can make them, that print the ANSI escape sequences that my `clear-scrollback` script (detailed below) uses to clear the screen. I will make them for all CPU architectures **officially** supported by the current release of Debian (i.e. Bullseye), because nine different architectures from 4 distinct families seems like a good challenge for someone who has never worked with assembly, let alone raw binary object code, which brings me to my next point - I won't be working in assembly - I'm skipping straight to machine code by hand, using the vi-like [hed](https://github.com/fr0zn/hed) hex editor, online information and documentation, the relevant man pages, and the header files, and nothing else whatsoever.


To test the executables, given that I only have access to computers running on a subset of the CPU architectures available, I will be testing them all on an amd64 system, taking advantage of the fact that the linux kernel can be configured to pass foreign binaries to userspace applications, like a Java runtime environment, a Python interpreter, wine, or QEMU. (This is dryly known as "Kernel Support for miscellaneous Binary Formats", or [binfmt-misc](https://www.kernel.org/doc/html/latest/admin-guide/binfmt-misc.html) for short.) I have been able to run the latest Debian Bullseye `busybox-static` builds from all nine architectures from an amd64 system.

If it turns out that this project is too simple, I am willing to expand the scope to include all "in-progress" Debian GNU/* builds (those targeting other architectures

### Why is so much of this centered around Debian, even though Debian has nothing to do with the project?
* I like Debian quite a lot. That's about all there is to it, to be honest.


## A few other things

I started this project already before even considering it as a possible capstone project. Thus far, all I done is created a git repostiory that, at time of writing, only has a README file and a Notes file in the `main` branch, and a private branch with an amd64 ELF "executable" that only consists of a header with no actual code. In my notes wrote extensively about the structure of an ELF header, including a field-by-field breakdown of the `busybox` (not `busybox-static`) amd64 Debian Bullseye build's header. It also includes my notes so far about the Linux ABI and the AMD64 architecture. The notes are available [here](https://github.com/eliminmax/tiny-clear-elf/blob/main/docs/Notes.md).

## Way too much info about the clear command's behavior that still leaves out a lot


### Testing just about every X11-compatible terminal emulator in the Debian Bullseye repos (and a framebuffer terminal, and a few multiplexers)
I have a shell script, which I wrote to have an alternative to `clear` that clears scrollback as well, using ANSI escape sequences, located on just about all of my systems at `/usr/local/bin/clear-scrollback`. Its code is as follows:

```sh
#!/bin/sh
printf "\033[H\033[J\033[3J"
```

Breaking it down, it prints 3 escape sequences:

1. `ESC[H`: moves the cursor to cell 0,0.
2. `ESC[J`: clears from cursor to end of screen
3. `ESC[3J`: clears the scrollback

whether or not the 3rd one works depends on the terminal emulator, but it has the behavior I would want in every case relevant to me.

`clear`'s behavior depends on the **terminfo** database's info on the terminal specified by the `$TERM` environment variable. Often, it includes the escape sequence `ESC[2J`, which clears the full screen. If the cursor is at the top-leftmost position, then `ESC[J` saves a character, not that it matters in most contexts.

#### How `clear` and `clear-scrollback` are handled by different terminal emulators, multiplexers, and consoles

 Terminals                                                                                                       |`$TERM` variable        | `clear` output hexdump     | ANSI sequences              |`clear-scrollback` results| Notes
-----------------------------------------------------------------------------------------------------------------|------------------------|----------------------------|-----------------------------|--------------------------|-
 `xterm`                                                                                                         |`xterm`                 | `1b5b481b5b324a1b5b334a`   | `ESC[H`, `ESC[2J`, `ESC[3J` | Worked as intended       | the classic X Terminal Emulator
 `pterm`                                                                                                         |`xterm`                 | `1b5b481b5b324a1b5b334a`   | `ESC[H`, `ESC[2J`, `ESC[3J` | Worked as intended       | the PuTTy port of xterm to Windows, ported to Linux
 `cool-retro-term`                                                                                               |`xterm`                 | `1b5b481b5b324a1b5b334a`   | `ESC[H`, `ESC[2J`, `ESC[3J` | Worked as intended       | emulates not just the behavior, but the looks of a classic computer terminal.
 `gnome-terminal` `xfce`4`-terminal` `lxterminal` `termonad` `mate` `terminal` `qterminal` `tilix` `sakura` `kgx`|`xterm-256color`        | `1b5b481b5b324a1b5b334a`   | `ESC[H`, `ESC[2J`, `ESC[3J` | Worked as intended       | All of these behaved the same on these tests, and none had anything else of note
 `yakuake` `console`                                                                                             |`xterm-256color`        | `1b5b481b5b324a1b5b334a`   | `ESC[H`, `ESC[2J`, `ESC[3J` | Worked as intended       | Both had issues where the cursor would render too far to the right after tab completion. Not surprising - yakuake is more-or-less just a drop-down version of konsole
 `terminology`                                                                                                   |`xterm-256color`        | `1b5b481b5b324a1b5b334a`   | `ESC[H`, `ESC[2J`, `ESC[3J` | Worked as intended       | terminology had an obnoxious gradient glare over the text, but was otherwise not at all notable.
 `Eterm`                                                                                                         |`Eterm`                 | `1b5b481b5b324a`           | `ESC[H`, `ESC[2J`           | Did not clear scrollback | 'Enlightened' terminal emulator - has a default background image that makes the text difficult to read
 `mlterm`                                                                                                        |`mlterm`                | `1b5b481b5b324a`           | `ESC[H`, `ESC[2J`           | Did not clear scrollback | mlterm - Multi Lingual TERMinal emulator on X
 `kitty`                                                                                                         |`xterm-kitty`           | `1b5b481b5b324a`           | `ESC[H`, `ESC[2J`           | Worked as intended       | A GPU-accelerated terminal emulator. Can be extended through 'kittens' written in python.
 `rxvt`                                                                                                          |`rxvt-unicode-256color` | `1b5b481b5b324a`           | `ESC[H`, `ESC[2J`           | Did not clear scrollback |
 `st`                                                                                                            |`st-256color`           | `1b5b481b5b324a`           | `ESC[H`, `ESC[2J`           | No scrollback support    | the suckless community's minimal terminal - not really relevent, as it does not even support scrollback without a patch
 `screen`                                                                                                        |`screen`                | `1b5b481b5b4a`             | `ESC[H`, `ESC[J`            | Worked as intended       | Had to clear scrollback both within screen and within the host terminal to get it to work.
 `tmux`                                                                                                          |`screen-256color`       | `1b5b481b5b4a`             | `ESC[H`, `ESC[J`            | Worked as intended       | Not much to say
 `tmux`                                                                                                          |`screen`                | `1b5b481b5b4a`             | `ESC[H`, `ESC[J`            | Worked as intended       | Terminal Multiplexer - normally, I configure it to use the `$TERM` value `screen-256color`, but I disabled that and ran the test again, to be more thorough than was reasonable for this.
 `byobu-tmux`                                                                                                    |`screen-256color`       | `1b5b481b5b4a`             | `ESC[H`, `ESC[J`            | Worked as intended       | Byobu is a wrapper for either tmux or screen that adds a bunch of eye candy and shortcuts that I find more intuitive. The `byobu-tmux` command forces it to use the tmux backend. Not surprsising that it gets the same results.
 `byobu-screen`                                                                                                  |`screen-256color-bce`   | `1b5b481b5b4a`             | `ESC[H`, `ESC[J`            | Worked as intended       | Surprised that the `$TERM` variable is different, but not that the other things are the same.
 `fbterm`                                                                                                        |`linux`                 | `1b5b481b5b4a1b5b334a`     | `ESC[H`, `ESC[J`, `ESC[3J`  | Did not clear scrollback | a terminal emulator that runs on a linux framebuffer
 `fbterm`                                                                                                        |`fbterm`                | `1b5b481b5b4a1b5b334a`     | `ESC[H`, `ESC[J`, `ESC[3J`  | Did not clear scrollback | fbterm does not set its `TERM` variable despite having proper `terminfo`, but setting it properly changes nothing.
 `terminal.app`                                                                                                  |`linux`                 | `1b5b481b5b4a1b5b334a`     | `ESC[H`, `ESC[J`, `ESC[3J`  | Did not clear scrollback | A terminal emulator for GNUstep environments. GNUstep is an LGPL reimplementation of Apple's Cocoa framework. As an aside, it created a folder called `GNUstep` in my `$HOME`. I can't stand when software makes a non-hidden file or directory in my `$HOME`! Get out of my `$HOME`!
 `fbcon` `vgacon`                                                                                                |`linux`                 | `1b5b481b5b4a1b5b334a`     | `ESC[H`, `ESC[J`, `ESC[3J`  | No scrollback support    | Part of the linux kernel. Versions before 5.9 had scrollback, but I'm on 5.10.

 in all tested terminal emulators, the `clear-scrollback` script at least cleared the screen, if not the scrollback as well.
