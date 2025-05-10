---
title: "Highmon"
date: "2009-08-02T23:47:03+00:00"
guid: http://www.longbowslair.co.uk/?page_id=722
aliases: /downloads/weechat-scripts/highmon/
---

Highmon is a perl script for Weechat 0.3.0+ that allows you to track highlighted messages in a new buffer. It is based off the chanmon system, and has similar features.
Current Version: **2.5** \[ [dl](http://dl.getdropbox.com/u/501502/highmon.pl) \]
(released: 16-08-2014)

Command Usage:

- /highmon \[help\] | \[monitor \[channel \[server\]\]\] | \[clean default|orphan|all\] | clearbar
  Command wrapper for highmon commands
- /highmon clean default|orphan|all will clean the config section of default 'on' entries, channels you are no longer joined, or both
- /highmon monitor \[channel\] \[server\] is used to toggle a highlight monitoring on and off, this can be used in the channel buffer for the channel you wish to toggle, or be given with arguments e.g. /monitor #weechat freenode
- /highmon clearbar will clear the contents of highmon's bar output

Settings:
'highmon' stores all it's settings in Weechat's plugins.conf, the following can be set there, by using /set or the 'iset' plug-in

- alignment - `/set plugins.var.perl.highmon.alignment "channel"`
  The config setting "alignment" can be changed to;
  "channel", "schannel", "nchannel", "channel,nick", "schannel,nick", "nchannel,nick"
  to change how the monitor appears
  The 'channel' value will show: "#weechat"
  The 'schannel' value will show: "6"
  The 'nchannel' value will show: "6:#weechat"
  (Default: 'channel')
- short\_names - `/set plugins.var.perl.highmon.short_names "on|off"`
  Setting this will trim the network name from chanmon, ala buffers.pl
  (Default: 'on')
- short\_names - `/set plugins.var.perl.highmon.merge_private`
  Setting this to 'on' will merge private messages to highmon's display
- color\_buf - `/set plugins.var.perl.highmon.color_buf "on|off|colour"`
  This turns coloured buffer names on or off, you can also set a single fixed colour by using a weechat colour name.
  This **must** be a valid colour name, or weechat will likely do unexpected things :)
  (Default: 'on')
- hotlist\_show - `/set plugins.var.perl.highmon.hotlist_show "on|off"`
  Setting this to 'on' will let the highmon buffer appear in hotlists (status bar/buffer.pl)
- away\_only - `/set plugins.var.perl.highmon.away_only "on|off"`
  Setting this to 'on' will only put messages in the highmon buffer when you set your status to away
- logging - `/set plugins.var.perl.highmon.logging`
  Toggles logging status for highmon buffer
  (default: off)
- output - `/set plugins.var.perl.highmon.output`
  Changes where output method of highmon; takes either "bar" or "buffer"
  (default; buffer)
- bar\_line - `/set plugins.var.perl.highmon.bar_lines`
  Changes the amount of lines the output bar will hold.
  (Only appears once output has been set to bar, defaults to 10)
- nick\_prefix - `/set plugins.var.perl.highmon.nick_prefix`
  nick\_suffix - `/set plugins.var.perl.highmon.nick_suffix`
  Sets the prefix and suffix chars in the highmon buffer
  (Defaults to <> if nothing set, and blank if there is)

Optional:

\# Set up tweaks; Hide the status and input lines on highmon `/set weechat.bar.status.conditions "${window.buffer.full_name} != perl.highmon" /set weechat.bar.input.conditions "${window.buffer.full_name} != perl.highmon"`

History:

- 2014-08-16, KenjiE20
  v2.5: -add: clearbar command to clear bar output
  -add: firstrun output prompt to check the help text for set up hints as they were being missed and update hint for conditions to use eval
  -change: Make all outputs use the date callback for more accurate timestamps (thanks Germainz)
- 2013-12-04, KenjiE20
  v2.4: -add: Support for eval style colour codes in time format used for bar output
- 2013-10-22, KenjiE20
  v2.3.3.2: -fix: Typo in fix command
- 2013-10-10, KenjiE20
  v2.3.3.1: -fix: Typo in closed buffer warning
- 2013-10-07, KenjiE20
  v2.3.3: -add: Warning and fixer for accidental buffer closes
- 2013-01-14, KenjiE20
  v2.3.2: -fix: Let bar output use the string set in weechat's config option
  -add: github info
- 2012-04-15, KenjiE20
  v2.3.1: -fix: Colour tags in bar timestamp string
- 2012-02-28, KenjiE20
  v2.3: -feature: Added merge\_private option to display private messages (default: off)
  -fix: Channel name colours now show correctly when set to on
- 2010-12-22, KenjiE20
  v2.2: -change: Use API instead of config to find channel colours, ready for 0.3.4 and 256 colours
- 2010-12-13, idl0r & KenjiE20
  v2.1.3: -fix: perl errors caused by bar line counter
  -fix: Add command list to inbuilt help
- 2010-09-30, KenjiE20
  v2.1.2: -fix: logging config was not correctly toggling back on (thanks to sleo for noticing)
  version sync with chanmon
- 2010-08-27, KenjiE20
  v2.1: -feature: Add 'nchannel' option to alignment to display buffer and name
- 2010-04-25, KenjiE20
  v2.0: Release as version 2.0
- 2010-04-24, KenjiE20
  v1.9: Rewrite for v2.0
  Bring feature set in line with chanmon 2.0
  -code change: Made more subs to shrink the code down in places
  -fix: Stop highmon attempting to double load/hook
  -fix: Add version dependant check for away status
- 2010-01-25, KenjiE20
  v1.7: -fixture: Let highmon be aware of nick\_prefix/suffix and allow custom prefix/suffix for chanmon buffer
  (Defaults to <> if nothing set, and blank if there is)
  (Thanks to m4v for this)
- 2009-09-07, KenjiE20
  v1.6: -feature: coloured buffer names
  -change: version sync with chanmon
- 2009-09-05, KenjiE20
  v1.2: -fix: disable buffer highlight
- 2009-09-02, KenjiE20
  v.1.1.1 -change: Stop unsightly text block on '/help'
- 2009-08-10, KenjiE20
  v1.1: In-client help added
- 2009-08-02, KenjiE20
  v1.0: Initial Public Release
