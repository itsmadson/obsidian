
- [ ] bash -c 'for output in $(xrandr --query | grep " connected" | cut -d" " -f1); do xrandr --output "$output" --gamma 1.15:1.05:0.95 --brightness 1; done'

- restore: xiccd& 
