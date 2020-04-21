# wmscripts
scripts I use with bspwm and sxhkd

## scratchpad terminal
To get a `quake`-like terminal that covers the top half of my screen, I use the following script
```
id=$(xdo id -n scratch);
if [ -z "$id" ]; then
    term scratch;
else
  action='hide';
  if [[ $(xprop -id $id | awk '/window state: / {print $3}') == 'Withdrawn' ]]; then
    action='show';
  fi
  xdo $action -n scratch
fi
```
coupled with this `bspwm` rule
```
bspc rule -a URxvt:scratch sticky=on state=floating rectangle=1908x540+0+0
```

