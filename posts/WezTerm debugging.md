How I debugged my WezTerm
=

[WezTerm](https://wezfurlong.org/wezterm/index.html) is a third party terminal emulator written by Wez Furlong.
I installed it because I needed a terminal that suppported true color and nerd font for Neovim to work as inteded.
It was very snappy and comfortable and I was very happy with it, up until the day that my alt button went rogue.
I wasn't getting brackets and braces anymore, I was getting nothing and then 8 or 9 of the next key I hit. This happened 
during a coding session and I still have no idea how this proverbial ghost in the shell actually got triggered.\
\
It was such a weird experience, I was convinced I had accidentally hit some secret macro that changed my keybindings. 
After digging through my config files to no avail I realized I had to start at the beginning and figure out what was actually
being sent to WezTerm when I tried using Alt.\
\
I found out that I could run WezTerm from a different terminal with key debugging enabled which would log every key press. This shed some light on the situation. 
```
> Char('(') SHIFT | ALT | LEFT_ALT -> send to pane Char('(') SHIFT | ALT | LEFT_ALT
```
The keys pressed were Shift+Alt+8. For some reason Shift+8 is logged as (, but Shift
 is also listed as an additional modifier, so Shift*2? So WezTerm can apparently see
 that I'm using modifiers, but doesn't seem to think that should affect the character
  being sent...\
\
So I decided to force it to. 
```
-- my .wezterm.lua
	keys = {
		{ key = "2", mods = "ALT", action = { SendKey = { key = "@" } } },
		{ key = "4", mods = "ALT", action = { SendKey = { key = "$" } } },
		{ key = "7", mods = "ALT", action = { SendKey = { key = "|" } } },
		{ key = "8", mods = "ALT", action = { SendKey = { key = "[" } } },
		{ key = "9", mods = "ALT", action = { SendKey = { key = "]" } } },
		{ key = "(", mods = "ALT|SHIFT", action = { SendKey = { key = "{" } } },
		{ key = ")", mods = "ALT|SHIFT", action = { SendKey = { key = "}" } } },
		{ key = "/", mods = "ALT|SHIFT", action = { SendKey = { key = "\\" } } },
	},

```
If you're a Windows person and find these bindings a bit weird it's because these are the standard bindings on Mac and they're just what I've become accustomed to. 

It turns out this spooky behaviour is due to some behind the scenes sloppy hacking 
on Apple's part. One day I'll get around to disabling SIP to go read the actual 
layout files, one day.