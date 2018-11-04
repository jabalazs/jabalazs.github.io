---
layout: post
title:  "NLP Development Workspace, Part 1: tmux"
date:   2018-07-30 11:02:00
categories:
---

In this post I'll explain the basics about tmux, a terminal multiplexer. "A
multiwhat?" Even I don't know what that means, but that's not important; as soon
as you see its potential its name won't matter anymore.

Have you ever had more than one terminal open? in more than one workspace?
connected to more than one remote server? tmux is here to save the day. Okay,
enough with the sales pitch.

Tmux is a program that runs terminal (is this true? what do I mean with
terminal? I know it's a server).

Tmux is a server capable of managing several terminal sessions. Your GUI
terminal connects to this server, and the server manages your stuff.

Tmux lets you have several terminal sessions open within only one GUI terminal
client, so instead of having this: [show image with lots of GUI terminals open],
you can have this: [show image with single tmux session open].

This not only lets you quickly navigate through your different terminal
sessions, but also makes it easier to think about [to make a mental model of],
the terminals you have open. [but it also makes it easier to think [build a
mental model of] your workspace]<sup>[1](#footnote:mental_model)</sup>.
<a name="footnote:mental_model.backlink"></a>I know this might not make much
sense now, but it will become clearer as we go through this tutorial.

[I know some of these commands might look ugly but trust me, when you realize
that you can easily customize them this won't be an issue.]

Before achieving this though, there are several things that you should know
first. If you feel comfortable with the tmux basics you can jump straight to the
[Commands](#commands) or [Customization](#customization) sections. For the rest,
tag along.

During this tutorial I'll be using tmux version 1.8 which is the one available
at [Ubuntu Universe](https://help.ubuntu.com/community/Repositories/Ubuntu) for
Ubuntu 14.04. Most things that I'll cover here apply to newer versions, and I'll
do my best to specify those that don't.

To verify whether you have tmux installed, and check the version you are
running, execute `tmux -V`. If it's not installed check the
[installation](#installation) section.

Finally, I'll try to make this post as accessible as possible to newcomers, so
please don't feel offended if some of the things I write seem _too_ obvious, I'm
no trying to insult anybody's intelligence :D

# Installing Tmux {#installation}

For those using Ubuntu, execute:

```bash
sudo apt-get update && sudo apt-get install tmux
```

For those using OS X, install [Homebrew](https://brew.sh/), then run:

```bash
brew install tmux
```

# Tmux Structure: Status Line, Sessions, Windows, and Panes {#structure}

Let's begin by talking about tmux's structure. When executing `tmux` you'll see
something like this:

![Bare-bones tmux session]({{ "/assets/images/raw_tmux_session.png" | absolute_url }})

You just initialized a tmux _session_, with an open _window_ containing a single
_pane_. I'll explain what all these things are in a jiffy. For now, let me
direct you to the green bar that popped up at the bottom of the screen. This is
called the "status line", and can display all sorts of information, but we'll
get to that later in the [Customization](#customization) section; for now let me
briefly explain its default structure. 


### Status Line {#status_line}

![Tmux status line explanation]({{ "/assets/images/tmux_status_line_explanation.svg" | absolute_url }})

As you can see in the image above, the status line is composed of 3 main zones:
`status-left`, "windows" area, and `status-right`. As we'll see in the
[Customization](#customization) section, you can directly customize
`status-left` and `status-right`, and change the way the windows' information is
displayed in the windows area.

**`status-left`**: The default behavior is for `status-left` to display only
the name of the current session within brackets (`0` by default). 

**windows area**: Right after opening tmux, you will see a single window listed
in the windows area indicated by `0:~*`. `0` corresponds to the window id, `~`
to your home directory (in general, this section will show the last directory
where a command was executed),  and `*` indicates which one is the
currently-active window. Head to the [next section](#prefix_and_windows) to see
what happens when you create a new window.

**`status-right`**: The `status-right` shows the
`pane_title`<sup>[2](#footnote:pane_title)</sup><a
name="footnote:pane_title.backlink"></a> and the current date in the format:
`%H:%M %d-%b-%y` (To know more about date formats take a look at the `FORMAT`
section of the [manpage for
`date`](http://linuxcommand.org/lc3_man_pages/date1.html)).  

The main point to keep in mind is the `status-line`'s overall structure, and not
what's being displayed at this point in particular, since you'll probably end up
[customizing](#customization) most things anyway.


### Prefix key and Windows {#prefix_and_windows}

Let's now create another window, but first let me explain what the
`prefix` key is. In order to execute commands in tmux, you first need to press
the prefix key, which by default is `C-b` (Ctrl+b). When you press this
combination you're telling tmux that you want to execute one of its commands,
instead of one from whatever other application you're using within your
terminal.
<sup>[3](#footnote:prefix_example)</sup><a name="footnote:prefix_example.backlink"></a>

Let's try this now: Press `C-b`, release the keys, then press `c`. You just
created another window, and should see something similar to the screenshot
below.


![New tmux window]({{ "/assets/images/new_window.png" | absolute_url }})

As you can see in the status bar, you just created window 1, and it became the
active window (as shown by `*`). This window works independently from the
first one, so it's just like having another terminal open.

You can also manually rename windows by pressing `C-b ,` (that is `<prefix> <comma>`),
deleting the current window name, and writing a new one. Try it!

So that's it for windows, pretty simple right? let's now create some panes.


### Panes {#panes}

A _pane_ is an independent terminal within a window. You can also think of them
as "splits", but "pane" is the official name.

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
to see their IDs you can press `C-b q` and you'll get something like this:

![tmux Pane IDS]({{ "/assets/images/pane_ids.png" | absolute_url }})

Pane IDs will be useful for the more advanced commands (such as changing panes
from one session to another), which we will cover in the [Tmux
Commands](#commands) section.

**Swapping Panes**

You can also swap panes around by pressing `C-b {` to swap the active pane with
the previous one, or `C-b }` to swap it with the next one.  After you do this,
the active pane will still be the one you were at before swapping.

![Swapping tmux panes]({{ "/assets/images/swapped_pane.png" | absolute_url }})

That's it for panes! They're just independent terminals embedded in a window.
Along with the `watch` command, they enable you to create GUI-like windows to
display all sorts of juicy data, like this:

PICTURE OF MY ENVIRONMENT

### Tmux Sessions

I mentioned earlier that after executing `tmux` you were in a window within a
session. Let's see now how we can interact with sessions and which benefits that
brings. Imagine you are writing some python code with a layout like this one:

![Some open tmux windows]({{ "assets/images/some_windows_open.png" | absolute_url }})

and you want to inialize a Jupyter notebook. You could, for example, achieve
this by opening a new window and executing it there. However, if you are
somewhat like me, you might not want to have those ugly server logs soiling your
beautiful workspace, but at the same time you might want to keep them somewhere
close. This is where sessions come in handy.

So let's do this: Let's _detach_ from the current session by pressing `C-b d`.
This will get you back to your tmuxless terminal, but do not fear! the tmux
server is still running in the background; all your windows are exactly as you
left them before detaching. We'll get back to them in a while.

Let's now execute `tmux new-session -s 2`. This will create a new session called
`2`, and drop you into it.

![New tmux session]({{ "assets/images/new_session.png" | absolute_url }})

You can now run a jupyter server with your favorite command within this
window 

![Jupyter notebook]({{ "assets/images/jupyter.png" | absolute_url }})

and go back to what you where working on on the previous session. To go back to
it you can press `C-b s` and a list of all your sessions will pop up:

![Session list]({{ "assets/images/session_list.png" | absolute_url }})

Here you can see a preview of the windows open within each each session, and
select one. Select session 0 to go back to what you were working on!

That's it for the basics. For most people this knowledge should be enough to
boost their productivity and ease the pain of having a gazillion terminal
clients or tabs open. Let's now dive into how to customize tmux, because hey,
begin practical doesn't mean it has to be ugly.

# Tmux Customization {#customization}

### Configuration files

By default tmux reads configuration options from two files: A system wide file
located in `/usr/local/etc/tmux.conf`, and a user-specific file located in
`~/.tmux.conf`<sup>[4](#footnote:home-dir)</sup><a
name="footnote:home-dir.backlink"></a><sup>,[5](#footnote:dotfiles)</sup><a name="footnote:dotfiles.backlink"></a>. 
If an option is specified in both files, the one defined in `~/.tmux.conf` will take precedence
<sup>[6](#footnote:sysadmin_note)</sup><a name="footnote:sysadmin_note.backlink"></a>.

Each line defined in your `.tmux.conf` file will be executed from top to bottom,
as if executing `tmux <command> <arguments>` from within a terminal, whenever a
tmux server is initialized.

### Remapping keybindings

#### Redefining the prefix key

Some people, myself included, recommend remapping the `prefix` key from `b` to
`a`.  This will of course depend on your own preferences and mappings, but in my
own experience doing this doesn't break anything important, and having the
prefix key closer to the `Ctrl` key feels more comfortable. If you like living
on the edge, you could go as far as remapping your `caps lock` key to `Ctrl` so
`Ctrl` and `a` are right next to each
other<sup>[7](#footnote:remapping_caps_lock)</sup><a
name="footnote:remapping_caps_lock.backlink"></a>. Who uses `caps lock` anyway?

Enough chatter. To remap your `prefix` key open the `~/.tmux.conf` file, or
create it if it doesn't exist already, and add the following lines:

```
set-option -g prefix C-a
unbind-key C-b
bind-key C-a send-prefix
```

<blockquote style="font-size:90%">

This is the method that appears in tmux's manpage, however I still don't
understand why we need both the <code style="font-size:90%">set-option -g prefix
C-a</code> and <code style="font-size:90%">bind-key C-a send-prefix</code>
options. I tested using only the first one, and everything
<strong>seemed</strong> to work, using only the second one did
<strong>not</strong> work, and using both did work. I advice against using only
the first option because I don't know whether that would have any unforeseen
side effects, so let's stick with what the manpage says.

</blockquote>

For the changes to take effect you need to restart the tmux server. To do that,
save the changes to the file, save all your work and **make sure you're not
running any vital processes within tmux**, and execute `tmux kill-server` in any
terminal window.  🚨**WARNING**🚨: this will kill every process running within your
tmux server, any unsaved changes will be lost. This will also remove any
sessions, windows and panes you created. We will learn how to define specific
tmux layouts in section [Tmuxinator: defining session layouts](#tmuxinator), so
you don't have to create your sessions, windows and panes from scratch each time
you reboot your computer.

After you execute `tmux` again, the `prefix` combination will be `C-a`; you can
do all we have learned so far by pressing `C-a` instead of the default `C-b`.

#### Keybinding for quickly reloading the `.tmux.conf` file

Now we want the changes we make to the `.tmux.conf` file to take effect without
having to restart the tmux server. To achieve this, we can define a keybinding
for re-executing the commands within our `.tmux.conf` file. To do this add the
following lines to `~/.tmux.conf`:

```
bind r source-file ~/.tmux.conf
```

and restart your tmux server again. Now whenever you press the `C-a r`
combination, the lines within your configuration file will be
executed<sup>[8](#footnote:sourcing-note)</sup><a
name="footnote:sourcing-note.backlink"></a>.

Note that this will only execute the commands within the `~/.tmux.conf` file,
and will not load any defaults, so, for example, if we previously executed an
`unbind` command, the key that was unbound will not be restored. To restore an
unbound key either specifically declare it in the config file, or restart the
tmux server.


* Remapping the prefix key to `C-a`
* Making windows 1-indexed instead of 0-indexed
* Using caps lock as ctrl
* Remapping pane creation to `|` and `-`
* Enabling mouse support
* Aesthetic changes
* Customizing `status-left` and `status-right`
* Some words on fonts(?) to have those nice powerline-like features

# Tmux Commands {#commands}

TALK ABOUT HOW TO SWAP PANES AND BREAK THEM, but do this in a section when we go
through more advanced commands in the `C-b :` interface

# Tmuxinator: defining session layouts {#tmuxinator}

# Tmux Versions {#versions}

<a href="https://twitter.com/intent/tweet?text={{ page.title }}&url={{ site.url }}{{ page.url }}&via={{ site.twitter_username }}&related={{ site.twitter_username }}" rel="nofollow" target="_blank" title="Share on Twitter">Twitter</a>

<a name="footnote:mental_model">1</a>: I really want to mention how easy it makes the
mental management of stuff. [[back](#footnote:mental_model.backlink)]

<a name="footnote:pane_title">2</a>: I will deliberately not talk about the
`pane_title` property since it is poorly documented and, in my opinion, does not
have any practical use. [[back](#footnote:pane_title.backlink)]

<a name="footnote:prefix_example">3</a>: For example, I use vim for my daily
development needs, and as some of you may know, vim has _lots_ of keybindings.
`prefix` avoids having conflicts between vim's and tmux's
keybindings. [[back](#footnote:prefix_example.backlink)]

<a name="footnote:home-dir">4</a>: Most Unix systems have something called "glob
expansion" which transforms specific glyphs into paths. In particular `~` gets
expanded to `/home/<username>` in Linux systems, and `/Users/<username>` in OSX,
where `<username>` corresponds to, you guessed, your username.
[[back](#footnote:home-dir.backlink)]

<a name="footnote:dotfiles">5</a>: Files whose name begin with a dot, such as
`.tmux.conf` are called _dotfiles_. Dotfiles live in your home directory `~` and
specify configuration options for almost every terminal-based software. Some
programs, however, create their own dotdirectories, such as `.jupyter` and
`.dropbox`, and might store their config files there. You'll have to refer to
each program's documentation to see how its configuration parameters are
defined. [[back](#footnote:dotfiles.backlink)]

<a name="footnote:sysadmin_note">6</a>: Unless you are a system administrator and want to provide a default
configuration for the users of the system, you should only modify
`~/.tmux.conf`. [[back](#footnote:sysadmin_note.backlink)]

<a name="footnote:remapping_caps_lock">7</a>: If you're on Ubuntu you can
achieve this by following this LINK TO TUTORIAL; for those using OSX LINK TO
ANOTHER TUTORIAL [[back](#footnote:remapping_caps_lock.backlink)]

<a name="footnote:sourcing-note">8</a>: This procedure is also known as
_sourcing_ a configuration file, hence the name `source-file` of the tmux
command. Those of you that have experimented modifying
the `.profile`, `.bash_profile`, or `.bashrc` files will probably be familiar
with this term. [[back](#footnote:sourcing-note.backlink)]


# Further reading
* https://hackernoon.com/a-gentle-introduction-to-tmux-8d784c404340
