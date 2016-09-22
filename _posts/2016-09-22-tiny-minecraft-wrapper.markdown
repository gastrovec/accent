---
title: Tiny Minecraft Wrapper
author: L3viathan
profile_picture: /images/l3viathan.png
about_stub: Recreational linguistics, distributional gastronomics, and applied galettalogy.
author_site: https://github.com/L3viathan
---

As an avid Minecraft player, I want to play on the most recent versions,
including snapshots. Things like Bukkit/Sponge/whatever it's called right now
are nice, but not compatible with snapshots.
[tinyMCW](https://github.com/L3viathan/tinyMCW/){:.capsule} is a wrapper around
Minecraft that can be extended to do some, but not all of the things Bukkit can
do.

The project is of course inspired by
[wurstminebot](https://github.com/wurstmineberg/wurstminebot), a bot that (when
it worked) was able to sync Minecraft chat to IRC, announce the time every
hour, and more. The bot hasn't worked properly for over a year, and when it
did, it required a complicated setup that can't really be done in a few
minutes.

In contrast, tinyMCW is not as powerful out-of-the-box, but can be extended
through a plugin interface to do many things wurstminebot never could. Setup is
simple:

1. Install `pexcept` using pip, if you haven't already.
2. Execute `python3 tinyMCW.py` in the same directory as
   `minecraft_server.jar`.

The plugin interface works by putting a python file in the `plugins/`
directory, and defining a dictionary called `_hooks` that maps predefined hook
strings to callables which are executed when certain events occur.

For instance, one of the plugins included is a [simple ping-pong
plugin](https://github.com/L3viathan/tinyMCW/blob/master/plugins/ping.py): It
hooks into the `command` event, which gets called whenever a player writes a
string starting with an exclamation mark in the chat. When the string that
follows is `"ping"`, the plugin instructs the server to respond with `"pong"`.
This way you know the system is working.

A more complex plugin can be used to strike a player with a lighting bolt: This
command takes an argument, and as you can see [in the
source](https://github.com/L3viathan/tinyMCW/blob/master/plugins/strike_player.py),
it isn't much more complicated either.

The full list of currently supported events is:

- "all" --- gets called on every line in the log
- "command" --- gets called when a chat entry begins with "!"
- "chat" --- gets called on any other chat message
- "say" --- gets called when `/say` is used (can be used to communicate
with command blocks)
- "join" --- gets called when a player joins the game
- "leave" --- gets called when a player leaves the game
- "shutdown" --- gets called when the server shuts down


As far as I know, noone except for me has ever used this. I exclusively used it
for a test server, and on it it worked very well.

This approach has certain limitations: At the moment it can only react to lines
in the log file (although it could be extended to call some plugins every _n_
seconds).  It cannot ever get to the extensibility of bukkitesque software (not
to mention Forge). But as a drop-in solution nothing I know beats its setup
speed.

As always, I'm open to [comments](https://github.com/L3viathan/tinyMCW/issues)
and [pull requests](https://github.com/L3viathan/tinyMCW/pulls).
