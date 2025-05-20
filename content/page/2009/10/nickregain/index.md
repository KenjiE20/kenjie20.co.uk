---
title: "nickregain"
date: "2009-10-19T14:36:01+00:00"
lastmod: "2010-12-13T18:24:55+00:00"
guid: http://www.longbowslair.co.uk/?page_id=806
aliases: /downloads/weechat-scripts/nickregain/
---

nickregain is a perl script for Weechat 0.3.0+ that checks if your current nick is your primary nick and attempts to recover it if it isn't.  
It does this by looking for nick quits and nick changes, and checked every x seconds.  
You can also specify a set of commands to issue to regain your primary nick instead.  
Current Version: **1.1.1** \[ [dl](http://dl.getdropbox.com/u/501502/nickregain.pl) \]  
(released: 25-04-2010)

Command Usage:

- /nickregain on|off|now|all  
  Set regain on or off for a server  
  'now' runs a one time test on current server  
  'all' runs a one time test on all servers

Settings:  
'nickregain' stores all it's settings in Weechat's plugins.conf, the following can be set there, by using /set or the 'iset' plug-in

- servername\_enabled - `/set plugins.var.perl.nickregain.servername_enabled`  
  This sets whether the nickregain is will run on a server  
  (Default: 'off')
- servername\_command - `/set plugins.var.perl.nickregain.servername_command`  
  Setting this will make nickregain issue this command instead of just "/nick " and override all other methods  
  You **WILL** need to add the '/nick' command to this  
  Use $nick to mark the nick, Commands can be separated using `;`  
  e.g `/msg nickserv ghost $nick;/nick $nick;/msg nickserv identify password`  
  See '/msg nickserv help' for exact syntax  
  (Default: blank)
- servername\_command\_delay - `/set plugins.var.perl.nickregain.servername_command_delay`  
  This sets the delay between the server connection and the command being triggered  
  (Default: 0)
- servername\_delay - `/set plugins.var.perl.nickregain.servername_delay`  
  This sets the delay between each /ison check  
  Used incase you can't see the old nick quit or nick change  
  (Default: '60')

History:

- 2010-12-13, idl0r  
  v1.1.1 -fix: corner case where $config{$name} didn't exist for a disconnecting server
- 2009-04-24, KenjiE20  
  v1.1 -feature: Add server\_command\_delay option to add a delay to server\_command  
  -fix: Broken quit/nick change detection  
  -fix: Close off leaking infolists  
  -fix: Hooks unhook properly now
- 2009-10-27, KenjiE20  
  v1.0.2  
  -fix: Make ison nicks check regexp quotemeta better, didn't always work  
  Give '/nickregain now' it's own sub, to trigger ison  
  Let /nickregain work out the server name, rather than assume it was on a server buffer
- 2009-10-22, KenjiE20  
  v1.0.1  
  -fix: make infolist loop $name's local vars, so they don't overwrite existing
- 2009-10-19, KenjiE20  
  v1.0RC: Public Release  
  -fix: /ison command was sending /ison /ison nicks  
  Quote the ison nicks check to protect regexp  
  Update settings on connect, fixes initial connected being set off  
  -code: Make creating the $isonnicks var less silly
- 2009-10-18, KenjiE20  
  v1.0: Initial Public Release Candidate
