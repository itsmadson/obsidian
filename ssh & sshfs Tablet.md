
ssh -p 2222 u0_a86@192.168.1.10 "mpv sdcard/Music/radiohead.mp3"

sshfs -p 2222 u0_a86@192.168.1.10:/storage/emulated/0/Music ~/tablet -o reconnect,ServerAliveInterval=15

cp ~/Downloads/.... ~/tablet/
