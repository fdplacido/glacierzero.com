---
title: "tmux: productive cli workflow"
date: 2021-11-24
images: ["/en/posts/img/tmux-9vQG6v1md1s.jpg"]
tags: ["terminal", "cli", "keyboard", "productivity"]
draft: false
---

[Tmux](https://github.com/tmux/tmux) has 2 main functions:
1. Creating, accessing and managing terminals from a single screen
2. Detaching and reattaching to the session: it allows you to continue were you left

Here I share the workflow I use everyday at work and my personal computer, explaining with it what I believe are the core features of tmux.

## Why tmux

`tmux` is a tool to organize yourself in your terminal:
- create and close windows
- names windows for easier organization
- split windows vertically and/or horizontally into panes as much as you want
- focus on some specific pane
- move around between windows and panes
- detach from a session and come back days later and continue everything were you left it
- share sessions between computers

Many of these things can be achieved with other tools, or combination of tools. Some terminals can split windows, in other you can open as many tabs or windows you want, other tools allow you to resume your work after closing a terminal.

But `tmux` is a central place to do all this, it is indepent from the application you use to access a terminal, and you can use it in remote servers to keep using your workflow. One only needs to learn the shortcuts, which work around a prefix key followed by some other key or keys: `ctrl + b` by default.

With it I can open any number of files in my text editor of choice, arranged in splits or windows however I prefer. I can be compiling some big project in one windows, then switch to other window to start a web server while the other window is compiling, then jump to a window with two files opened split vertically to compare them. And at the end of the day I can just detach, and continue were I left the day after: same arrangement, windows.

Long running tasks like compilation or tests may be left running in background without a remote session being killed for inactivity.


## Sessions

Sessions allow to manage groups of windows, one can connect and disconnect from these, like changing projects or workspaces. I almost never use more than one session: I just connect and work with the same session.

```sh
# Create new named session
tmux new -s <session name>

# attach to named session
tmux a -t <name>
tmux attach -t <name>

# detach from session
prefix + d

# attach to last used session
tmux a
```

Whenever I start a terminal I automatically connect to it through my `.bashrc` file with the following:

```sh
#######################################################
# Load tmux on first terminal
#######################################################
# 1. check existance of tmux command
# 2. check for interactive shell (PS1)
# 3. Avoid tmux (and screen) running within itself
if command -v tmux &> /dev/null && [ -n "$PS1" ] && [[ ! "$TERM" =~ screen ]] && [[ ! "$TERM" =~ tmux ]] && [ -z "$TMUX" ]; then
  # with exec command, on exiting tmux the terminal is closed
  # also with exec, on opening a second window no duplicate session is catched
  # on this config, new window can be run from the terminal `st -t "name"`
  # exec tmux new -s server

  # alternatively just run tmux (but duplicate session detected)
  tmux new -s server

fi
```

## Windows

Tmux windows are equivalent to tabs in web browsers, it allows you to open multiple command line prompts. Each window can hold multiple [panes](#panes), but can also be used without them.

There are different ways of switching between windows:
- With `prefix + 0...9`: input the number assigned to the window
  + Limited to 10 windows, since only one digit is used to change between them
- With `prefix + '`, then input the number
  + Takes an extra step, but allows to enter any number even if more than one digit is used
- With `prefix + w`: shows a list of windows and preview of each window panes (numbered panes)
  + This option gives the most info at a glance, and allows for intuitive switching with arrow keys (or vim-style keys `hjkl`)
  + Sub panes can also be accessed with arrow keys for a fine-grained selection of prompt

```sh
# create new window
prefix + c

# switch to window
prefix + 0 ... 9
### switch inputing number
prefix + `
<number>
### switch showing list
prefix + w

# cycle through windows
prefix + n
prefix + p

# rename window
prefix + ,

# kill window
prefix + &
```

## Panes

- Panes can be used to display multiple terminals in the same window. Side by side terminals or complex configurations can be used here by splitting the window vertically and horizontally
- Panes can be zoomed in, so one focuses for a moment on it, then goes back to seeing all the panes together
- Panes can also be resized to give more space to some specific terminal


```sh
# split horizontal - omit last " symbol
prefix + "                                     "

# split vertical
prefix + %

# move to panel
prefix + <arrow key>

# cycle through panels
prefix + o

# kill current panel
prefix + x

# resize panels
prefix + alt + <arrow>

# zoom current pane
prefix + z

# Balance size panels vertically
prefix + Alt + 2

# Balance size panels horizontally
prefix + Alt + 1
```
