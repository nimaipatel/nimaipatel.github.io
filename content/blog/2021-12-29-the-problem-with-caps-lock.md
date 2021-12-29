<!-- title: The Problem with Caps Lock -->
<!-- author: Nimai Patel -->

The `Caps Lock` key is probably the most useless key on modern computer
keyboards. However, it is placed in one of the most convenient positions in the
home row. Not mapping it to anything else is just wasting its potential. What I
like to do is map `Caps Lock` by itself to the `Esc` key and `Caps Lock` +
`<key>` to `Ctrl` + `<key>`.

I then map `shift` + `Caps Lock` to `Caps Lock` for the rare occasions on which
I do need to use it.

I achieve this using a script which depends on `xcape` and `xmodmap` (assuming
you're using a US keyboard layout):

```
#!/bin/sh
setxkbmap -layout us
killall xcape 1>/dev/null 2>&1
xmodmap -e 'clear lock'
xmodmap -e 'clear control'
xmodmap -e 'add control = Caps_Lock Control_L Control_R'
xmodmap -e 'keycode 66 = Control_L Caps_Lock NoSymbol NoSymbol'
xcape   -e 'Control_L=Escape'
```

This way I can achieve:
```
CapsLock => Esc
CapsLock + <Key> => Ctrl + <Key>
Shift + CapsLock => CapsLock
```

# But... Why?

Well, `Esc` and `Ctrl` keys are two of the most utilizable keys. Ironically,
they are placed in the worst possible place on a keyboard. `Esc` is used for
switching from insert mode to normal mode in vim and `Ctrl` is the most commonly
used modifier in most programs. 

I have been using this setup for months now, and I'm sure my wrists would thank
me for it if they could.
