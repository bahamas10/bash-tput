bash-tput
=========

A drop-in replacement for `tput` in Bash scripts that is extremely limited and
opinionated.

Usage
-----

You can use this code in 3 main ways:

### 1. Copy + Paste

This is the easiest and most straight-forward - just copy and paste the code
into your existing Bash script:

``` bash
tput() {
    ...
}

# your script goes here
```

### 2. Source this file

Source the file if it exists in a known-location on your filesystem:

``` bash
. ./tput

# your script goes here
```

### 3. Execute this script directly

You can just run this script directly to test it yourself:

```
$ ./tput setaf 1; echo hi
hi
```

NOTE: this completely negates the speed-benefit of calling this from a script
but it's great for testing.

Reasoning
---------

This code was originally written for a Bash TUI called
[Nightfall](https://nightfall.ysap.sh).  In profiling that code it took around
~600 calls to `tput` to render the initial UI and that ended up being very slow
on some systems.  Other systems (FreeBSD for example) just didn't work with some
of the `tput` commands not being supported.

I decided to "wrap" `tput` in a Bash function that would bake-in as much of
the functionality I needed and then fork out to the external `tput` command
when needed.  This Brought the number of forked `tput` calls to 0 to draw the
UI which sped the program up immensely and resulted in FreeBSD working as
well.

Synopsis
--------

`tput(1)` is a command that offers "Portable Terminal Control" but, in
practice, is not very portable.  Posix specifies it must exist and support a
small number of options but isn't very specific about its usage and command
naming.

A number of sources were surveyed to see what kind of usage is out there in
the wild:
- `tput` provided by Linux (via `ncurses`)
- `tput.c` on FreeBSD
- `tput.c` on illumos
- `tput` usage on MacOS

There is a large number of overlap in both arguments and commands but there
are still some differences (notably `setaf` vs `AF` on FreeBSD, or the lack of
`-S` and `-V` support on some operating systems).

Goal
----

The goal of this project is to provide a drop-in replacement function that can
be sourced (or copy and pasted) into existing Bash scripts and they will just
reap the benefits of a more uniform `tput` command for outputting terminal
control characters.  This will make the scripts execute much faster as well as
work across more operating system independent version of `tput`.

Philosophy
----------

This version of `tput` should NOT break anything - it should just optimize for
the common case of outputting terminal control characters.  When this version
of `tput` can NOT do something itself it will instead opt to fork out to the
external version of `tput` found.

Because of this, this version will accept arguments like `-T <term>` but
completely ignore them (unless passed to the external `tput` command) and
instead *always* opt to output ANSI escape sequences.

References
----------

- https://nightfall.ysap.sh
- https://github.com/dylanaraps/pure-bash-bible?tab=readme-ov-file#escape-sequences
- https://github.com/fidian/ansi
- https://linuxcommand.org/lc3_adv_tput.php
- https://www.gnu.org/software/termutils/manual/termutils-2.0/html_chapter/tput_1.html
- https://pubs.opengroup.org/onlinepubs/009696899/utilities/tput.html

License
-------

MIT License
