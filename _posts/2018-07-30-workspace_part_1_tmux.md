---
layout: post
title:  "NLP Development Workspace, Part 1: tmux"
date:   2018-07-30 11:02:00
categories:
---


Tmux lets you have several terminal sessions open within only one GUI terminal
client, so instead of having lots of terminal windows open, like this: 

![Lots of Terminal Windows
Open](/assets/images/tmux_blogpost/1-lots-of-iterms-open.png "Lots of Terminal Windows
Open")

you can have this:

![Single tmux session](/assets/images/tmux_blogpost/2-tmux-preview.png "Single tmux session")

This not only lets you quickly navigate through your different terminal
sessions, but also makes it easier to think about
the terminals you have open. I know this might not make much
sense now, but it will become clearer as we go through this tutorial.

<!-- TODO: make a list with advantages of using tmux -->
<!-- * Lots of terminal opens -->
<!-- * tidy -->
<!-- * better think about open terminals -->

<!-- * single ssh connection to a remote server, but several terminals -->
<!-- * keep stuff running even after connection is killed -->

<!-- Another advantage is that tmux lets you have a single open ssh connection to -->
<!-- a remote server with as many "terminals" as you want. -->

I'll be using tmux version 3.1 which is the latest available at the time of
writing.  To verify whether you have tmux installed, and check the version you
are running, execute `tmux -V`. If it's not installed check the
[installation](#installation) section.

Finally, I'll try to make this post as accessible as possible to newcomers, so
please don't feel offended if some of the things I write seem _too_ obvious.

If you feel comfortable with the tmux basics you can jump straight to the
[Commands](#commands) or [Customization](#customization) sections. For the rest,
tag along.

# Installing Tmux {#installation}

For those using OS X or Linux, install [Homebrew](https://brew.sh/) (same
instructions for both OS's), then run:

```bash
brew install tmux
```

If you are using Ubuntu and want to use the built-in package manager, execute:

```bash
sudo apt-get update && sudo apt-get install tmux
```

However, this will install a version of tmux depending on your release (e.g.
tmux 3.0 on Ubuntu 20.04 Focal Fossa. Click
[here](https://packages.ubuntu.com/focal/tmux) for more details.)

You can also build tmux from source if you feel like it. Follow the instructions
[here](https://github.com/tmux/tmux#installation).


# **Tmux Structure: Status Line, Sessions, Windows, and Panes** {#structure}

Let's begin by talking about tmux's structure. When executing `tmux` you'll see
something like this:

![Bare-bones tmux session]({{ "/assets/images/tmux_blogpost/3-landing.png" | absolute_url }})

You just initialized a tmux _session_, with an open _window_ containing a single
_pane_. I'll explain what all these things are in a jiffy. For now, let me
direct you to the green bar that popped up at the bottom of the screen. This is
called the "status line", and can display all sorts of information, but we'll
get to that later in the [Customization](#customization) section; for now let me
briefly explain its default structure. 


## Status Line {#status_line}

![Tmux status line explanation]({{ "/assets/images/tmux_blogpost/4-tmux_status_line_explanation.svg" | absolute_url }})

As you can see in the image above, the status line is composed of 3 main zones:
`status-left`, "window list", and `status-right`. As we'll see in the
[Customization](#customization) section, you can directly customize
`status-left` and `status-right`, and change the way the windows' information is
displayed in the window list.

**`status-left`**: The default behavior is for `status-left` to display only
the name of the current session within brackets (`0` by default). 

**window list**: Right after opening tmux, you will see a single window listed
in the window list indicated by `0:bash*`. `0` corresponds to the window id, `:`
is just a separator, `bash` is the `window-name` which by default corresponds to
the last executed process within the window, and `*` indicates which one is the
currently-active window. Head to the [next section](#prefix_and_windows) to see
what happens when you create a new window.

**`status-right`**: The `status-right` shows the `window
title`<sup>[1](#footnote:window_title)</sup><a
name="footnote:window_title.backlink"></a> and the current date in the format:
`%H:%M %d-%b-%y` (To know more about date formats take a look at the `FORMAT`
section of the [manpage for
`date`](http://linuxcommand.org/lc3_man_pages/date1.html)).

The main point to keep in mind is the `status-line`'s overall structure, and not
what's being displayed at this point in particular, since you'll probably end up
[customizing](#customization) most things anyway.


## Prefix key and Windows {#prefix_and_windows}

Let's now create another window, but first let me explain what the
`prefix` key is. In order to execute commands in tmux, you first need to press
the prefix key, which by default is `C-b` (Ctrl+b). When you press this
combination you're telling tmux that you want to execute one of its commands,
instead of one from whatever other application you're using within your
terminal.

Let's try this now: Press `C-b`, release the keys, then press `c`. You just
created another window, and should see something similar to the screenshot
below.


![New tmux window]({{ "/assets/images/tmux_blogpost/5-new_window.png" | absolute_url }})

As you can see in the status bar, you just created window 1, and it became the
active window (as shown by `*`). This window works independently from the
first one, so it's just like having another terminal open.

You can also manually rename windows by pressing `C-b ,` (that is `<prefix> <comma>`),
deleting the current window name, and writing a new one. Try it!

So that's it for windows, pretty simple right? let's now create some panes.


## Panes {#panes}

A _pane_ is an independent terminal within a window. You can also think of them
as "splits", but "pane" is the official name.

**Vertical Panes** 

Press `C-b %` to create a vertical pane; you should get something like this:

![New vertical pane]({{ "/assets/images/tmux_blogpost/6-vertical_pane.png" | absolute_url }})

You can easily switch between panes by pressing `C-b left-arrow` or `C-b
right-arrow`. You can also see that the separator bar looks green at the bottom
half; this means that the second pane (right) is selected. If you move to the
first pane, you'll see how the top half becomes green.


**Horizontal Panes**

Now, why stop at vertical panes? Press `C-b "` to create a horizontal pane
within the currently-selected pane.

![New horizontal pane]({{ "/assets/images/tmux_blogpost/7-horizontal_pane.png" | absolute_url }})

I know these default commands might be hard to remember but bear with me; in
section [More intuitive keybindings](#intuitive) we'll see how to remap them.

**Pane Indexes**

There are also a few tricks that you can do with panes. For example if you want
to see their IDs you can press `C-b q` and you'll get something like this:

![tmux Pane IDS]({{ "/assets/images/tmux_blogpost/8-pane_ids.png" | absolute_url }})

Pane IDs will be useful for the more advanced commands (such as changing panes
from one session to another), which we will cover in the [Tmux
Commands](#commands) section.


**Swapping Panes**

You can also swap panes around by pressing `C-b {` to swap the active pane with
the previous one, or `C-b }` to swap it with the next one.  After you do this,
the active pane will still be the one you were at before swapping.

![Swapping tmux panes]({{ "/assets/images/tmux_blogpost/9-swapped_pane.png" | absolute_url }})


**Resizing Panes**

You can easily resize panes by pressing `C-b C-<arrow-key>` for small
size increments and `C-b M-<arrow-key>` for bigger size increments.

`M-` stands for "meta", but in most keyboards this corresponds to the `Alt` key
<sup>[2](#footnote:meta_links)</sup><a name="footnote:meta_links.backlink"></a>.
Try it! press `C-b M-<down arrow>` a couple of times to resize the current pane.

![Resizing tmux panes]({{ "/assets/images/tmux_blogpost/10-resized_pane.png" | absolute_url }})


That's it for panes! They're just independent terminals embedded in a window.

<!-- TODO: Put this in a different subsection at the end -->

<!-- Along with the `watch` command, they enable you to create GUI-like windows to -->
<!-- display all sorts of juicy data, like this: -->

<!-- PICTURE OF MY ENVIRONMENT -->




## Tmux Sessions

I mentioned earlier that after executing `tmux` you were in a window within a
session. Let's see now how we can interact with sessions and which benefits that
brings. Imagine you are writing some python code with a layout like this one:

![Some open tmux windows]({{ "/assets/images/tmux_blogpost/12-some_windows_open.png" | absolute_url }})

and you want to inialize a Jupyter notebook server. You could, for example, achieve
this by opening a new window and executing it there. However, if you are
somewhat like me, you might not want to have those ugly server logs soiling your
beautiful workspace, but at the same time you might want to keep them somewhere
close. This is where sessions come in handy.

So let's do this: Let's _detach_ from the current session by pressing `C-b d`.
This will get you back to your tmuxless terminal, but do not fear! the tmux
server is still running in the background; all your windows are exactly as you
left them before detaching. We'll get back to them in a while.

Let's now execute `tmux new-session -s new`. This will create a new session
called `new`, and drop you into it.

![New tmux session]({{ "/assets/images/tmux_blogpost/13-new_session.png" | absolute_url }})

You can now run a jupyter server with your favorite command within this
window 

![Jupyter notebook]({{ "/assets/images/tmux_blogpost/14-jupyter.png" | absolute_url }})

and go back to what you where working on on the previous session. To go back to
it you can press <span style="white-space: nowrap;">`C-b s`</span> and a list of
all your sessions will pop up:

![Session list]({{ "/assets/images/tmux_blogpost/15-session_list.png" | absolute_url }})

Here you can see a complete list of the sessions within the tmux server. Select
session 0 to go back to what you were working on!

When navigating the list you can also press the `<right arrow>` key to expand a
session and see the windows open within it:

![Expanded session list]({{ "/assets/images/tmux_blogpost/16-expanded_session_list.png" | absolute_url }})

That's it for the basics. For most people this knowledge should be enough to
boost their productivity and ease the pain of having a gazillion terminal
clients or tabs open. 

In summary:

![Annotated session]({{ "/assets/images/tmux_blogpost/11-resized_pane_annotated.svg" | absolute_url }})

Let's now dive into how to customize tmux, because hey, being practical doesn't
mean it has to be ugly.


# **Tmux Customization** {#customization}

**Disclaimer**: I borrowed most of the ideas in this section from all over the
internet. By now I don't remember what came from where but I am listing most of
the resources I have used in the [Further Reading](#further-reading) section. If
you find the source of something I didn't link to, please let me know and I will
update the article!


## Configuration files

By default tmux reads configuration options from two files: A system wide file
located in `/usr/local/etc/tmux.conf`, and a user-specific file located in
`~/.tmux.conf`<sup>[3](#footnote:home-dir)<a
name="footnote:home-dir.backlink"></a>,[4](#footnote:dotfiles)</sup><a
name="footnote:dotfiles.backlink"></a>.  If an option is specified in both
files, the one defined in `~/.tmux.conf` will take precedence
<sup>[5](#footnote:sysadmin_note)</sup><a
name="footnote:sysadmin_note.backlink"></a>. From now on I'll only talk about
`~/.tmux.conf`, and will not touch the system-wide config file.

Whenever a tmux server is initialized, each line defined in your `.tmux.conf`
file will be executed from top to bottom, as if executing

```
tmux <command> <arguments>
```

from within a terminal.

## Remapping keybindings

### Redefining the prefix key

Some people, myself included, recommend remapping the `prefix` key from `b` to
`a`.  This will of course depend on your own preferences and mappings, but in my
own experience doing this doesn't break anything important, and having the
prefix key closer to the `Ctrl` key feels more comfortable. If you like living
on the edge, you could go as far as remapping your `caps lock` key to `Ctrl` so
`Ctrl` and `a` are right next to each
other<sup>[6](#footnote:remapping_caps_lock)</sup><a
name="footnote:remapping_caps_lock.backlink"></a>. Who uses `caps lock` anyway? <span style="font-size:50%">It's fine if you do, whatever floats your boat :)</span>

To remap your `prefix` key open the `.tmux.conf` file, or create it if it
doesn't exist already, and add the following lines:

```
set-option -g prefix C-a
unbind-key C-b
bind-key C-a send-prefix
```

The first line will set Ctrl-a as the new prefix combination and the second will
unset the default Ctrl-b. The third line is used for sending the prefix signal
into whatever program is running within tmux. This is specially useful when
running nested tmux sessions; the first press of `C-a` will be sent to the local
session, and the second, to the nested one. See
[this](https://unix.stackexchange.com/questions/367557/binding-prefix-key-in-tmux-twice-really-needed)
stackexchange question and accepted answer.

For the changes to take effect you either need to restart the tmux server, or
execute the following series of commands from anywhere within tmux: first press
`C-b :` to enter tmux's command prompt. In the prompt type `source-file
~/.tmux.conf` to reload the configuration file (we'll talk more about tmux's
command promt in the [Tmux Commands](#commands) section). Now your new prefix
key is `C-a`.

### Keybinding for quickly reloading the `.tmux.conf` file

Now we want the changes we make to the `.tmux.conf` file to take effect without
having to restart the tmux server or explicitly write the `source-file` command.
To achieve this, we can define a keybinding for re-executing the commands within
our `.tmux.conf` file. To do this add the following lines to `.tmux.conf`:

```
bind r source-file ~/.tmux.conf
```

where `bind` is equivalent to `bind-key` (aka an
[_alias_](https://en.wikipedia.org/wiki/Alias_(command))). Then, either do the
`C-a :`, <span style="white-space: nowrap;">`source-file ~/.tmux.conf`</span>
conjuration again, or restart your tmux server. Now whenever you press the `C-a
r` combination, the lines within your configuration file will be
executed<sup>[7](#footnote:sourcing-note)</sup><a
name="footnote:sourcing-note.backlink"></a>.

Note that this will only execute the commands within the `.tmux.conf` file,
and will not load any defaults, so, for example, if we previously executed an
`unbind` command, the key that was unbound will not be restored. To restore an
unbound key either specifically declare it in the config file, or restart the
tmux server.

Optionally we can make tmux output a message to let us know that we sourced the
configuration file by modifying the previous instruction:

```
bind r source-file ~/.tmux.conf \; display ".tmux.conf reloaded"
```

This will bind the <span style="white-space: nowrap;">`source-file ~/.tmux.conf
; display ".tmux.conf reloaded"`</span> _command sequence_, to the `C-a r`
combination. Note that we have to escape the semicolon (`\;`) to prevent tmux
from interpeting this line as two separate instructions when starting up. If you
don't escape it, tmux will try to execute `display ".tmux.conf reloaded"` before
the display area is created, resulting in an error. 


### More intuitive pane keybindings {#intuitive}

#### Splitting

I find the default keybindings for splitting into vertical panes (`C-a %`) and
horizontal panes (`C-a "`) not very intuitive, which is why I like to remap them
to something easier to remember: `C-a -` for horizontal splits and `C-a |` for
vertical ones. Just add the following lines to your `.tmux.conf`:

```
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %
```

#### Switching

I also like having easier ways to switching between panes. One of these is
having `Alt <arrow-key>` instead of `C-a <arrow-key>`, that is, moving between
panes without having to press the `prefix` key. Add these lines to the
`.tmux.conf` file:

```
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D
```

Where the flag `-n` tells the `bind` command to make these keybindings not
require the `prefix`.


It is also nice to have vim keybindings for changing panes without having to
move away from the home row:

```
bind h select-pane -L
bind l select-pane -R
bind k select-pane -U
bind j select-pane -D
```

Note that these commands _will_ require a press of the prefix key.

### Enabling mouse support

If you want to use your mouse to interact with tmux, for changing windows or
resizing panes, for example, you can easily enable mouse support by adding the
following line to your `.tmux.conf`:

```
set -g mouse on
```

### Making windows and panes 1-indexed

I find it rather unintuitive that windows start from 0 instead of 1. I like my
windows to be arranged in a way that matches my keyboard layout. If you think
like this as well, add the following line to your config file:

```
set -g base-index 1
```

You can achieve the same effect on pane numbering with the command:

```
set -g pane-base-index 1
```

## Aesthetic Improvements

Ok! Now using tmux should feel a little more comfortable. Now let's make it look
better.


* Aesthetic changes
* Customizing `status-left` and `status-right`
* Prefix Indicator
* Some words on fonts(?) to have those nice powerline-like features

# **Tmux Commands** {#commands}

TALK ABOUT HOW TO SWAP PANES AND BREAK THEM

# **Tmuxinator: Defining Session Layouts** {#tmuxinator}


<a href="https://twitter.com/intent/tweet?text={{ page.title }}&url={{ site.url }}{{ page.url }}&via={{ site.twitter_username }}&related={{ site.twitter_username }}" rel="nofollow" target="_blank" title="Share on Twitter">Twitter</a>

<a name="footnote:window_title">1</a>: According to tmux's
[manpage](http://man7.org/linux/man-pages/man1/tmux.1.html#NAMES_AND_TITLES),
tmux distinguishes between names and titles, which might be confusing unless
you're developing an application which interacts with the terminal it is running
in. In this tutorial we will only deal with session and window names, so we can
ignore window titles. For the more curious ones, just know that the window title
corresponds to the active pane title, which by default is the name of the host
tmux is running in. Check Names and Titles section of the tmux
[manpage](http://man7.org/linux/man-pages/man1/tmux.1.html#NAMES_AND_TITLES),
and [this](https://stackoverflow.com/a/9757133/3941813) stackoverflow answer to
learn more. <a href="#footnote:window_title.backlink" style="display: inline;">↩</a>


<a name="footnote:meta_links">2</a>: If you want to read more on this, check
[this](https://en.wikipedia.org/wiki/Meta_key) Wikipedia article, some info on
the space cadet keyboard
([here](https://en.wikipedia.org/wiki/Space-cadet_keyboard) and
[here](http://www.catb.org/jargon/html/S/space-cadet-keyboard.html)), and
[this](https://askubuntu.com/a/19565/351183) askubuntu answer.
[↩](#footnote:meta_links.backlink)

<a name="footnote:home-dir">3</a>: Most Unix systems have something called
[_tilde
expansion_](https://www.gnu.org/software/bash/manual/html_node/Tilde-Expansion.html)
which transforms `~` into `/home/<username>` in Linux systems, and
`/Users/<username>` in OSX, where `<username>` corresponds to, you guessed, your
username. More specifically, `~` expands into whatever your `HOME` [environment
variable](https://en.wikipedia.org/wiki/Environment_variable) is.  Another
concept similar to tilde expansion is called _glob expansion_. This defines
other symbols to be used in paths and automatically expanded by your
[shell](https://en.wikipedia.org/wiki/Shell_(computing)). Read more about glob
expansions in [this](https://en.wikipedia.org/wiki/Glob_(programming)) wikipedia
article, and in the glob
[manpage](http://man7.org/linux/man-pages/man7/glob.7.html).
[↩](#footnote:home-dir.backlink)

<a name="footnote:dotfiles">4</a>: Files whose name begin with a dot, such as
`.tmux.conf` are called _dotfiles_. Dotfiles live in your home directory `~` and
specify configuration options for almost every terminal-based software. Some
programs, however, create their own dotdirectories, such as `.jupyter` and
`.dropbox`, and might store their config files there. You'll have to refer to
each program's documentation to see how its configuration parameters are
defined. [↩](#footnote:dotfiles.backlink)

<a name="footnote:sysadmin_note">5</a>: Unless you are a system administrator and want to provide a default
configuration for the users of the system, you should only modify
`~/.tmux.conf`. [↩](#footnote:sysadmin_note.backlink)

<a name="footnote:remapping_caps_lock">6</a>: If you're on Ubuntu you can
achieve this by following this LINK TO TUTORIAL; for those using OSX LINK TO
ANOTHER TUTORIAL [↩](#footnote:remapping_caps_lock.backlink)

<a name="footnote:sourcing-note">7</a>: This procedure is also known as
_sourcing_ a configuration file, hence the name `source-file` of the tmux
command. Those of you that have experimented modifying
the `.profile`, `.bash_profile`, or `.bashrc` files will probably be familiar
with this term. [↩](#footnote:sourcing-note.backlink)


# What I left out

* Tmux Plugins: because I haven't needed them so far and I don't think they're
  essential. If you're interested in learning more about them, check the `tpm`
  [repo](https://github.com/tmux-plugins/tpm) (short for tmux plugin manager),
  and the [tmux-plugins](https://github.com/tmux-plugins) github organization.

# Further reading {#further-reading}
* [https://hackernoon.com/a-gentle-introduction-to-tmux-8d784c404340](https://hackernoon.com/a-gentle-introduction-to-tmux-8d784c404340)
* [https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)
* [https://danielmiessler.com/study/tmux/](https://danielmiessler.com/study/tmux/)
* [https://medium.com/actualize-network/a-minimalist-guide-to-tmux-13675fb160fa](https://medium.com/actualize-network/a-minimalist-guide-to-tmux-13675fb160fa)
* [https://edricteo.com/tmux-tutorial/](https://edricteo.com/tmux-tutorial/)
* [https://www.codementor.io/bruno/beginner-s-guide-to-tmux-recommended-configuration-plugins-and-navigation-demo-aih7o7ktw](https://www.codementor.io/bruno/beginner-s-guide-to-tmux-recommended-configuration-plugins-and-navigation-demo-aih7o7ktw)
* [https://pragprog.com/book/bhtmux2/tmux-2](https://pragprog.com/book/bhtmux2/tmux-2)
* [https://medium.freecodecamp.org/tmux-in-practice-local-and-nested-remote-tmux-sessions-4f7ba5db8795](https://medium.freecodecamp.org/tmux-in-practice-local-and-nested-remote-tmux-sessions-4f7ba5db8795)
* [https://leanpub.com/the-tao-of-tmux/read](https://leanpub.com/the-tao-of-tmux/read)

# Some dotfiles
* [https://github.com/gpakosz/.tmux](https://github.com/gpakosz/.tmux)
