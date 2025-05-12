---
title: "Chanmon"
date: "2009-07-21T13:58:47+00:00"
lastmod: "2014-08-16T16:42:23+01:00"
guid: http://www.longbowslair.co.uk/?page_id=458
aliases: /downloads/weechat-scripts/chanmon/
---

Chanmon is a perl script for Weechat 0.3.0+ that allows you to track IRC channels in a new buffer, without merging them using Weechat's buffer merge feature
Current Version: **2.5** \[ [dl](http://dl.getdropbox.com/u/501502/chanmon.pl) \]
(released: 16-08-2014)

Command Usage:

- /chanmon \[help\] | \[monitor \[channel \[server\]\]\] | \[dynmon\] | \[clean default|orphan|all\] | clearbar
  Command wrapper for chanmon commands
- /monitor \[channel\] \[server\] is used to toggle a channel monitoring on and off, this can be used in the channel buffer for the channel you wish to toggle, or be given with arguments e.g. /monitor #weechat freenode
- /dynmon is used to toggle 'Dynamic Channel Monitoring' on and off, this will automagically stop monitoring the current active buffer, without affecting regular settings (Default is off)
- /chanclean default|orphan|all will clean the config section of default 'on' entries, channels you are no longer joined, or both
- /chanmon clearbar will clear the contents of chanmon's bar output

Hidden toggles:
'chanmon' stores all it's settings in Weechat's plugins.conf, the following can be set there, by using /set or the 'iset' plug-in

- alignment - `/set plugins.var.perl.chanmon.alignment "channel"`
  The config setting "alignment" can be changed to;
  "channel", "schannel", "nchannel", "channel,nick", "schannel,nick", "nchannel,nick"
  to change how the monitor appears
  The 'channel' value will show: "#weechat"
  The 'schannel' value will show: "6"
  The 'nchannel' value will show: "6:#weechat"
  (Default: 'channel')
- short\_names - `/set plugins.var.perl.chanmon.short_names "on|off"`
  Setting this will trim the network name from chanmon, ala buffers.pl
  (Default: 'on')
- merge\_private - `/set plugins.var.perl.chanmon.merge_private`
  Setting this to 'on' will merge private messages to chanmon's display
- color\_buf - `/set plugins.var.perl.chanmon.color_buf "on|off|colour"`
  This turns coloured buffer names on or off, you can also set a single fixed colour by using a weechat colour name.
  This **must** be a valid colour name, or weechat will likely do unexpected things :)
  (Default: 'on')
- show\_aways - `/set plugins.var.perl.chanmon.show_aways "on|off"`
  Toggles showing the Weechat away messages
  (Default: 'off')
- logging - `/set plugins.var.perl.chanmon.logging "on|off"`
  Toggles logging status for chanmon buffer
  (default: off)
- output - `/set plugins.var.perl.chanmon.output`
  Changes where output method of chanmon; takes either "bar" or "buffer"
  (default; buffer)
- bar\_lines - `/set plugins.var.perl.chanmon.bar_lines`
  Changes the amount of lines the output bar will hold.
  (Only appears once output has been set to bar, defaults to 10)
- nick\_prefix - `/set plugins.var.perl.chanmon.nick_prefix`
  nick\_suffix - `/set plugins.var.perl.chanmon.nick_suffix`
  Sets the prefix and suffix chars in the chanmon buffer
  (Defaults to <> if nothing set, and blank if there is)
- servername.#channel
  servername is the internal name for the server (set when you use /server add)
  #channel is the channel name, (where # is whatever channel type that channel happens to be)

Optional:

\# Ideal set up:
\# Split the layout 70/30 (or there abouts) horizontally and load
\# Optionally, hide the status and input lines on chanmon
`/window splith 70 –> open the chanmon buffer
/set weechat.bar.status.conditions "${window.buffer.full_name} != perl.chanmon"
/set weechat.bar.input.conditions "${window.buffer.full_name} != perl.chanmon".bar.status.conditions “active”`

History:

- 2014-08-16, KenjiE20
  v2.5: -add: clearbar command to clear bar output
  -add: firstrun output prompt to check the help text for set up hints as they were being missed and update hint for conditions to use eval
  -change: Make all outputs use the date callback for more accurate timestamps (thanks Germainz)
- 2013-12-04, KenjiE20
  v2.4: -add: Support for eval style colour codes in time format used for bar output
- 2013-10-10, KenjiE20
  v2.3.3.1: -fix: Typo in closed buffer warning
- 2013-10-07, KenjiE20
  v2.3.3: -add: Warning and fixer for accidental buffer closes
- 2013-01-14, KenjiE20
  v2.3.2: -fix: Let bar output use the string set in weechat's config option
  -add: github info
  -change: Ideal set up -> Example set up
- 2012-04-15, KenjiE20
  v2.3.1: -fix: Colour tags in bar timestamp string, bar error fixes from highmon
- 2012-02-28, KenjiE20
  v2.3: -feature: Added merge\_private option to display private messages (default: off)
- 2010-12-22, KenjiE20
  v2.2: -change: Use API instead of config to find channel colours, ready for 0.3.4 and 256 colours
- 2010-12-05, KenjiE20
  v2.1.3: change: /monitor is now /chmonitor to avoid command conflicts (thanks m4v)
  (/chanmon monitor remains the same)
  -fix: Add command list to inbuilt help
- 2010-09-30, KenjiE20
  v2.1.2: -fix: logging config was not correctly toggling back on (thanks to sleo for noticing)
- 2010-09-20, m4v
  v2.1.1: -fix: chanmon wasn't detecting buffers displayed on more than one window
- 2010-08-27, KenjiE20
  v2.1: -feature: Add 'nchannel' option to alignment to display buffer and name
- 2010-04-25, KenjiE20
  v2.0: Release as version 2.0
- 2010-04-24, KenjiE20
  -fix: No longer using hard-coded detection for ACTION and TOPIC messages. Use config settings for ACTION printing
- 2010-04-15, KenjiE20
  v1.9: Rewrite for v2.0
  -feature: /monitor takes arguments
  -feature: Added /chanclean for config cleanup
  -feature: Buffer logging option (default: off)
  -feature: Selectable output (Bar/Buffer (default))
  -feature:
  - /chanmon is now a command wrapper for all commands
  - /help chanmon gives command help
  - /chanmon help gives config help
  -code change: Made more subs to shrink the code down in places
  -fix: Stop chanmon attempting to double load/hook
- 2010-02-10, m4v
  v1.7.1: -fix: chanmon was leaking infolists, changed how chanmon detects if the buffer is displayed or not.
- 2010-01-25, KenjiE20
  v1.7: -fixture: Let chanmon be aware of nick\_prefix/suffix and allow custom prefix/suffix for chanmon buffer
  (Defaults to <> if nothing set, and blank if there is)
  -fix: Make dynamic monitoring aware of multiple windows rather than just the active buffer
  (Thanks to m4v for these)
- 2009-09-07, KenjiE20
  v1.6: -feature: coloured buffer names
  -change: highmon version sync
- 2009-09-05, KenjiE20
  v1.5: -fix: disable buffer highlight
- 2009-09-02, KenjiE20
  v.1.4.1 -change: Stop unsightly text block on '/help'
- 2009-08-10, KenjiE20
  v1.4: -feature: In-client help added
  -fix: Added missing help entries
  Fix remaining ugly vars
- 2009-07-09, KenjiE20
  v.1.3.3 -fix: highlight on the channel monitor when someone /me highlights
- 2009-07-04, KenjiE20
  v.1.3.2 -fix: use new away\_info tag instead of ugly regexp for away detection
  -code: cleanup old raw callback arguement variables to nice neat named ones
- 2009-07-04, KenjiE20
  v.1.3.1 -feature(tte): Hide /away messages by default, change 'show\_aways' to get them back
- 2009-07-01, KenjiE20
  v.1.3 -feature(tte): Mimic buffers.pl 'short\_names'
- 2009-06-29, KenjiE20
  v.1.2.1 -fix: let the /monitor message respect the alignment setting
- 2009-06-19, KenjiE20
  v.1.2 -feature(tte): Customisable alignment
  Thanks to 'FreakGaurd' for the idea
- 2009-06-14, KenjiE20
  v.1.1.2 -fix: don't assume chanmon buffer needs creating
  fixes crashing with /upgrade
- 2009-06-13, KenjiE20
  v.1.1.1 -code: change from True/False to on/off for weechat consistency
  Settings WILL NEED to be changed manually from previous versions
- 2009-06-13, KenjiE20
  v1.1: -feature: Dynamic Channel Monitoring,
  don't display messages from active channel buffer
  defaults to Disabled
  Thanks to 'sjohnson' for the idea
  -fix: don't set config entries for non-channels
  -fix: don't assume all channels are #
- 2009-06-12, KenjiE20
  v1.0.1: -fix: glitch with tabs in IRC messages
- 2009-06-10, KenjiE20
  v1.0: Initial Public Release
