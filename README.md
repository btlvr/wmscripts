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

## smart preselection
```
for pid in $(pidof -x smart_presel_move); do
    if [ $pid != $$ ]; then
        exit 1
    fi
done

function sm() {
	SRAT=$1
	SDIR=$(bspc query -T -n focused.local | tr ',' '\n' | grep splitDir | cut -d':' -f3 | tr -d '"')
	if [ ! -z "$SDIR" ]; then
		if [ "$SDIR" == "south" ] || [ "$SDIR" == "east" ]; then
			SRAT=`bc <<< "1-$SRAT"`
		fi
		bspc node -o $SRAT
	fi
}

SRAT=$(bspc query -T -n focused | tr ',' '\n' | grep splitRatio | grep "}$" | cut -d':' -f2 | tr -d '}')
SDIR=$(bspc query -T -n focused.local | tr ',' '\n' | grep splitDir | cut -d':' -f3 | tr -d '"')
NEWDIR=$1

function adjusted_rat() {
	if [ ! -z "$SDIR" ]; then
		if ([ "$SDIR" == "south" ] || [ "$SDIR" == "east" ]); then
			SRAT=`bc <<< "1-$SRAT"`
		fi
		echo $SRAT
	fi
}

adjusted_rat
bspc node -p cancel
bspc node -p $NEWDIR
sm $SRAT
```

```
SRAT=$1
SDIR=$(bspc query -T -n focused.local | tr ',' '\n' | grep splitDir | cut -d':' -f3 | tr -d '"')

if [ ! -z "$SDIR" ]; then
	if [ "$SDIR" == "south" ] || [ "$SDIR" == "east" ]; then
		SRAT=`bc <<< "1-$SRAT"`
	fi
	bspc node -o $SRAT
fi
```

## safe transfer
```
focus=$(bspc query -N -n focused)

if [ ! -z "$focus" ]; then
	bspc node -d $1
	if [ "$2" == "focus" ]; then
		bspc node -f $focus
	fi
fi
```
