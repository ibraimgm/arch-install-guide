<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-generate-toc again -->
**Table of Contents**

- [Arch Linux Install Guide / Checklist](#arch-linux-install-guide--checklist)
    - [Basic installation](#basic-installation)
    - [Configuring graphical mode](#configuring-graphical-mode)
    - [Network](#network)
    - [Aura](#aura)
    - [Audio](#audio)
    - [Screensaver and background](#screensaver-and-background)
    - [Keyboard "BEEP" on X](#keyboard-beep-on-x)
    - [Numpad Home/End keys](#numpad-homeend-keys)
    - [Backligth](#backligth)
    - [Annoying lecture when using sudo](#annoying-lecture-when-using-sudo)
    - [Thunar](#thunar)
    - [Sample configuration files](#sample-configuration-files)
        - [/etc/X11/xorg.conf.d/10-evdev.conf](#etcx11xorgconfd10-evdevconf)
        - [~/.xinitrc (relevant part only)](#xinitrc-relevant-part-only)
        - [~/.bash_profile](#bashprofile)
        - [~/.bashrc](#bashrc)
        - [~/.profile](#profile)
        - [~/.Xresources](#xresources)

<!-- markdown-toc end -->


# Arch Linux Install Guide / Checklist #

This is a *personal* checkilst for the installation of arch linux on my personal
machines. While some of the content here is useful in *any* installation, keep
in mind that the fous is biased towards my personal preference.


## Basic installation ##

To install the base system, the best option issimply to use
the [Archlinux Ultimate Install](https://github.com/helmuthdu/aui). The script
itself is very easy to follow and takes away most of the boring/annoying
configuration steps in a new install.

For desktop environment I choose `fluxbox`, and for the bootloader, `grub`. No
graphical login is chosen.

## Configuring graphical mode ##

After everything is said and done, boot on the new system and make sure you **do
not** have a `.xinirc` (or at least is is commented) and try running
`startx`. If everything is OK, you will see a rather ugly X environment. Close
everything and return to console mode.

Now, try to run fluxbox, putting `exec fluxbox` in your `.xinirc` and try
`startx` again. If it works, we're good!

Exit fluxbox and to to the terminal again. This time, install `xdm` and start
(do not enable it yet) it's service with `systemctl start xdm`. Try `startx`
again and see if you can login.

If th XDM login works, just enable the service and reset, to see if everything
is truly working as intended.

**TIP:** If you are stuck and cannot login with yout user, try logging with
root.

## Network ##

First, make sure you have installed NetworkManager. Now, install
`network-manager-applet` and start this together with fluxbox with `nm-applet &`.

## Aura ##

Since some of the packages I use are from the AUR, it is useful to take a time
to install `aura-bin`. Just do `yaourt -i aura-bin`.

Note that `aura` can also be used as a "recovery point" tool, with the `-B`
switch.

## Audio ##

To make audio easier to configure, install `pasystray` via AUR and init it
together with fluxbox. Take a look at
the [official repository](https://github.com/christophgysin/pasystray) and
install any other required tooks (ex. `pavucontrol`).

## Screensaver and background ##

Install `feh` and `xscreensaver`. For Feh, just take a look at the sample
configuration (below), and to choose the screen saver, run `xscreensaver-demo`.

## Keyboard "BEEP" on X ##

Just put `xset b off` on your `.xinirc`, as
recommended
[here](https://unix.stackexchange.com/questions/39518/turn-off-beep-of-xorg).

## Numpad Home/End keys ##

To make the numpad Home/End keys works when you use shift, add
`numpad:microsoft` to your XOrg configuration, as
seen
[here](https://askubuntu.com/questions/57079/xubuntu-make-shiftnumpad-work-like-windows).

## Backligth ##
The easier thing to do is to install `xorg-backligth` and bind it to the
relevant keys. Take a look at
my [personal fluxbox configuration](https://github.com/ibraimgm/dot-fluxbox) for
examples.

Remember to use `xev` to discover the correct key codes.

## Annoying lecture when using sudo ##

After the installation, sudo is configured to **always** give you a lecture on
evvery command you use. To make it do the lecture only once, try puttin
`Defaults lecture=once` somewhere on your sudoers, like this example:

```
## Read drop-in files from /etc/sudoers.d
## (the '#' here does not indicate a comment)
#includedir /etc/sudoers.d
Defaults !requiretty, !tty_tickets, !umask
Defaults visiblepw, path_info, insults, lecture=once
Defaults loglinelen=0, logfile =/var/log/sudo.log, log_year, log_host, syslog=auth
Defaults passwd_tries=3, passwd_timeout=1
Defaults env_reset, always_set_home, set_home, set_logname
Defaults !env_editor, editor="/usr/bin/vim:/usr/bin/vi:/usr/bin/nano"
Defaults timestamp_timeout=15
Defaults passprompt="[sudo] password for %u: "
```

## Thunar ##

Make sure you
follow
[the official documentation](https://wiki.archlinux.org/index.php/thunar#Starting_in_daemon_mode),
specially the part about starting in *daemon mode*.

The first time you start thunar, it will be ugly as a sin. To make things more
bearable, install some themes (you might want to use AUI script to do this) and
`lxappearance`. Just run it and choose a theme (myfavorite is *Arc*).

## Sample configuration files ##

### /etc/X11/xorg.conf.d/10-evdev.conf ###

```
Section "InputClass"
Identifier "evdev keyboard catchall"
MatchIsKeyboard "on"
MatchDevicePath "/dev/input/event*"
Driver "evdev"
Option "XkbLayout" "us"
Option "XkbVariant" "alt-intl"
Option "XkbOptions" "numpad:microsoft"
EndSection
```

### ~/.xinitrc (relevant part only) ###

```
xset b off &
nm-applet &
pasystray &
thunar --daemon &
dropbox &
xscreensaver -no-splash &
feh --randomize --bg-scale ~/.fluxbox/wallpapers/* &
exec startfluxbox
```

### ~/.bash_profile ###

```
#
# ~/.bash_profile
#

[[ -f ~/.bashrc ]] && . ~/.bashrc
```

### ~/.bashrc ###

```
#
# ~/.bashrc
#

# If not running interactively, don't do anything
[[ $- != *i* ]] && return

alias ls='ls --color=auto'
PS1='[\u@\h \W]\$ '

source ~/.profile
```

### ~/.profile ###

```
export PROFILE_IS_LOADED=1

export LC_CTYPE=pt_BR.UTF-8
#export GTK_IM_MODULE=ibus

source /usr/share/git/completion/git-prompt.sh
source /usr/share/git/completion/git-completion.bash

# non-printable characters must be enclosed inside \[ and \]
PS1='\[\033]0;$MSYSTEM:\w\007\]' # set window title
PS1="$PS1"'\n'                 # new line
PS1="$PS1"'\[\033[32m\]'       # change color
PS1="$PS1"'\u@\h '             # user@host<space>
PS1="$PS1"'\[\033[33m\]'       # change color
PS1="$PS1"'\w'                 # current working directory
if test -z "$WINELOADERNOEXEC"
then
    PS1="$PS1"'$(__git_ps1)'   # bash function
fi
PS1="$PS1"'\[\033[0m\]'        # change color
PS1="$PS1"'\n'                 # new line
PS1="$PS1"'$ '                 # prompt: always $

export GIT_PS1_SHOWDIRTYSTATE=1
export GIT_PS1_SHOWSTASHSTATE=1
export GIT_PS1_SHOWUNTRACKEDFILES=1
export GIT_PS1_SHOWCOLORHINTS=1
export GIT_PS1_SHOWUPSTREAM="auto"

# aliased commands
alias ls='ls --color'
alias ref='sudo reflector -c BR -c US -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist'

# fix libreoffice gtk3 bugs (gtk3 is experimental)
export SAL_USE_VCLPLUGIN=gtk
```

### ~/.Xresources ###

```
URxvt.geometry: 180x35
! URxvt.transparent:   true
! URxvt.shading:       40

!font
Xft.dpi: 96
URxvt*font: xft:Bitstream Vera Sans Mono:size=10
! *font: xft:M+1mn:size=12
! *barFont: xft:M+1mn:size=12
! *iconFont: xft:FontAwesome:size=12

!font rendering
Xft.antialias: true
Xft.autohint: false
Xft.hinting: true
Xft.hintstyle: hintfull
Xft.lcdfilter: none
Xft.rgba: rgba

! Colors
URxvt*background: #000000
URxvt*foreground: #B2B2B2
! black
URxvt*color0:  #000000
URxvt*color8:  #686868
! red
URxvt*color1:  #B21818
URxvt*color9:  #FF5454
! green
URxvt*color2:  #18B218
URxvt*color10: #54FF54
! yellow
URxvt*color3:  #B26818
URxvt*color11: #FFFF54
! blue
URxvt*color4:  #1818B2
URxvt*color12: #5454FF
! purple
URxvt*color5:  #B218B2
URxvt*color13: #FF54FF
! cyan
URxvt*color6:  #18B2B2
URxvt*color14: #54FFFF
! white
URxvt*color7:  #B2B2B2
URxvt*color15: #FFFFFF
```
