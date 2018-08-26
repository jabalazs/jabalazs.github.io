---
layout: post
title:  "NLP Development Workspace, Part 1: tmux"
date:   2018-07-30 11:02:00
categories:
---

<p style="text-align:justify"> In this post I'll explain the basics about
tmux, a terminal multiplexer.  "A multiwhat?" Even I don't know what that means,
but that's not important; as soon as you see its potential its name won't matter
anymore.
</p>


<p style="text-align:justify">
Have you ever had more than one terminal open? in more than one workspace?
connected to more than one remote server? tmux is here to save the day. Okay,
enough with the sales pitch.
</p>

<p style="text-align:justify">
Tmux is a program that runs  terminal (is this true? what do I mean with
terminal? I know it's a server).
</p>

<p style="text-align:justify">
Tmux is a server capable of managing several terminal sessions. Your GUI
terminal connects to this server, and the server manages your stuff.
</p>

<p style="text-align:justify">
Tmux lets you have several terminal sessions open within only one GUI terminal
client, so instead of having this: [show image with lots of GUI terminals open],
you can have this: [show image with single tmux session open].
</p>

<p style="text-align:justify">
This not only lets you quickly navigate through your different terminal
sessions, but also makes it easier to think about [to make a mental model of],
the terminals you have open. [but it also makes it easier to think [build a
mental model of] your workspace]<sup>[1](#myfootnote1)</sup>. I know this might
not make much sense now, but it will become clearer as we go through this
tutorial.
</p>


<div style="text-align:justify">
Before achieving this though, there are several things that you should know
first. If you feel comfortable with the tmux basics you can jump to [NEXT
SECTION], for the rest, tag along.
</div>

# Tmux structure: Sessions, Windows, and Panes

When executing `tmux` you'll see something like this:

![Bare-bones tmux session]({{ "/assets/images/raw_tmux_session.png" | absolute_url }})

You just initialized a tmux _session_, with an open _window_ containing a single
_pane_. Now, let me direct you to the green bar that popped up in the bottom of
the screen. This is called the "status line", and can display all sorts of
information, but we'll get to that later; for now let me briefly explain
its default structure. 

![Tmux status line explanation]({{ "/assets/images/tmux_status_line_explanation.svg" | absolute_url }})

### Status Line

As you can see in the image above, the status line is composed by 3 main zones:
`status-left`, "windows area", and `status-right`. As we'll see in section
CUSTOMIZATION SECTION, you can directly customize `status-left` and
`status-right`, and customize the way windows' information is displayed in the
"windows area".

The default behavior is for `status-left` to show you only the name of the
current session within brackets (`0` by default). 

The "windows area" shows all the windows belonging to the current session, but
right after opening tmux you'll only see one, indicated by `0:~*`. `0` is the
window id, `~` corresponds to your home directory, but in general this section
will show the last directory whithin the window where a command was executed.
Finally the asterisk `*` indicates that this is the currently-active window. See
the next section to see what happens when you create a new window.

The `status-right` shows the `pane_title` and the current date in the format:
`%H:%M %d-%b-%y` (If you want to know more about date formats take a look at the
[manpage for `date`](http://linuxcommand.org/lc3_man_pages/date1.html))



### Prefix key and Windows

Let's now create another window, but first let me first explain what the
`prefix` key is. In order to enter tmux "command mode" your first need to press
the prefix key, which by default is `C-b` (Ctrl+b). Let's try this now: Press
`C-b`, release the keys, then press `c`. TA-DAH, you just created another
window as shown below.

![New tmux window]({{ "/assets/images/new_window.png" | absolute_url }})

As you can see in the status bar, you just created window 1, and it became
active (as shown by the `*` symbol next to it.). This new window works
independently from the first one, so it's just like having another terminal
open.

So that's it for windows, pretty simple right? let's now create some panes.


### Panes

A _pane_ is an independent terminal within a window. You can also think of them as
"splits", but "pane" is the official name.

**Vertical Panes** 

Press `C-b %` to create a vertical pane; you should get something like this:

![New vertical pane]({{ "/assets/images/vertical_pane.png" | absolute_url }})

You can easily switch between panes by pressing `C-b left-arrow` or `C-b
right-arrow`. You can also see that the separator bar looks green at the bottom
half; this means that the second pane (right) is selected. If you move to the
first pane, you'll see how the top half becomes green.

**Horizontal Panes**

Now, why stop at vertical panes? Press `C-b "` to create a horizontal pane
within the currently-selected pane.

![New horizontal pane]({{ "/assets/images/horizontal_pane.png" | absolute_url }})

**Pane Indexes**

There are also a few tricks that you can do with panes. For example if you want
to see their ids you can press `C-b q` and you'll get something like this:

![tmux Pane IDS]({{ "/assets/images/pane_ids.png" | absolute_url }})

**Swapping Panes**

You can also swap panes around by pressing `C-b {` to swap the active pane with
the previous one, or `C-b }` to swap it with the next one.  After you do this,
the active pane will still be the one you were at before swapping.

![Swapping tmux panes]({{ "/assets/images/swapped_pane.png" | absolute_url }})

TALK ABOUT HOW TO SWAP PANES AND BREAK THEM, but do this in a section when we go
through more advanced commands in the `C-b :` interface


That's it for panes! They're just independent terminals embedded in a window.



<a name="myfootnote1">1</a>: I really want to mention how easy it makes the
mental management of stuff



