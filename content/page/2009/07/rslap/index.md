---
title: "rslap"
date: "2009-07-21T13:57:41+00:00"
lastmod: "2010-12-30T20:39:49+00:00"
guid: http://www.longbowslair.co.uk/?page_id=485
aliases: /downloads/weechat-scripts/rslap/
---

rslap is a perl script for Weechat 0.3.0+ that allows you to slap a nick with a random, predefined, message
Current Version: **1.3.1** \[ [dl](http://files.getdropbox.com/u/501502/rslap.pl) \]
(released: 25-04-2010)

Command Usage:

- /rslap \[ will pick a random message and issue a /me in the current buffer, entry will use that entry number instead of a random one
- /rslap\_info is used to print out the available messages rslap can use
- /rslap\_add
  /rslap\_remove
  Adds / removes string/id from the available list and attempts to update the rslap file

Settings:

- slapback - `/set plugins.var.perl.rslap.slapback "off|on/random/n"
  Sets the slapback, where n is a valid entry number`

`'rslap'' stores all it's messages in a plain text file called 'rslap' in Weechat's config dir.
Each line in the file is a separate message.
Use $nick to denote where the nick should go
A default file is generated with the following, if no rslap file is available;
``` slaps $nick around a bit with a large trout
gives $nick a clout round the head with a fresh copy of WeeChat
slaps $nick with a large smelly trout
breaks out the slapping rod and looks sternly at $nick
slaps $nick's bottom and grins cheekily
slaps $nick a few times
slaps $nick and starts getting carried away
would slap $nick, but is not being violent today
gives $nick a hearty slap
finds the closest large object and gives $nick a slap with it
likes slapping people and randomly picks $nick to slap
dusts off a kitchen towel and slaps it at $nick ```
History:
- 2010-12-30, KenjiE20
v1.3.1 -fix: uninitialised value error
- 2010-04-25, KenjiE20
v1.3 -feature: Ability to add/remove entries
-feature: Can specify which string /rslap will use
-feature: Slapback with specified/random string
- 2009-08-10, KenjiE20 :
v1.2: Correct /help format to match weechat base
- 2009-07-28, KenjiE20 :
v1.1: -fix: Make file loading more robust
and strip out comments/blank lines
- 2009-07-09, KenjiE20 :
v1.0: Initial Public Release
`
