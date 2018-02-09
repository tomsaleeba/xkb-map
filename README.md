This is still a work in progress, I'm learning how to create the maps.

## What I know so far

Check what files are being used to make the current map:
```bash
$ setxkbmap -query -verbose 10
Setting verbose level to 10
locale is C
Trying to load rules file ./rules/evdev...
Trying to load rules file /usr/share/X11/xkb/rules/evdev...
Success.
Applied rules from evdev:
rules:      evdev
model:      pc105
layout:     us
options:    grp:shift_caps_toggle
Trying to build keymap using the following components:
keycodes:   evdev+aliases(qwerty)
types:      complete
compat:     complete
symbols:    pc+us+inet(evdev)+group(shift_caps_toggle)
geometry:   pc(pc105)
rules:      evdev
model:      pc105
layout:     us
options:    grp:shift_caps_toggle
```

How to change `<caps lock>` to `<tab>`:
```bash
cd /usr/share/X11/xkb/symbols
sudo nano pc # because geometry = pc(pc105) above
# inside the xkb_symbols "pc105" section, find "key <CAPS>"
# change it to : key <CAPS> {        [ Tab, Caps_Lock             ]       };
```

Apparently, according to https://wiki.debian.org/Keyboard, you can get the settings picked up with:
```bash
sudo dpkg-reconfigure xkb-data
service keyboard-setup restart
udevadm trigger --subsystem-match=input --action=change
```
...but that doesn't work for me. Using the following does work though:
```bash
sudo dpkg-reconfigure keyboard-configuration
```
Alternatively, a full X restart will make the changes take effect to. You might be able to use `ctrl+alt+backspace` to restart X.

After the reload, the unshifted caps lock key should be a tab, and shifted it should have the original caps lock behaviour.


You can get altgr+j=home and altgr+l=end by editing `/usr/share/X11/xkb/symbols/us` with the following patch:
```patch
39c39
<     key <AC07> {	[	  j,	J, Home		]	};
---
>     key <AC07> {	[	  j,	J		]	};
41c41
<     key <AC09> {	[	  l,	L, End		]	};
---
>     key <AC09> {	[	  l,	L		]	};
```

Then you need to know your AltGr key. Run `sudo dpkg-reconfigure keyboard-configuration` and select an AltGr key. After, you'll have the mapped keys.

# New map or modify

It's probably better to create a new map (TODO figure out how to do that) and switch to it, so you don't destroy the base map incase you have to revert. If you also use a keyboard with programmable keys, you'll probably mess that up because you've remapped the scan codes. Having a separate map will let you quickly switch mappings to suit the keyboard.
