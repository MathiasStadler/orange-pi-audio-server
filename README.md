# orange-pi-audio-server

## housekeeping

```txt
 docker run -it -d -p 8083:8080 -v "/home/trapapa/playground/orange-pi-audio-server:/home/coder/project" -u "$(id -u):$(id -g)" codercom/code-server:latest --cert
git config --global user.name "Mathias Stadler"
git config --global user.EMAIL "email@mathias-stadler.de"
# https://help.github.com/en/github/using-git/caching-your-github-password-in-git
git config --global credential.helper 'cache --timeout=3600'
## enable paste in terminal
# step:1 , make sure code server is running on "https://" not http , add --cert for auto generated self sign cert
# step:2, In Chrome set clipboard permission go to chrome://settings/content/clipboard and add your server url
# step3: now ctrl + v will work in code server

```

## source

```txt
#
https://github.com/TheBiggerGuy/docker-pulseaudio-example/blob/master/pulse-client.conf
#
https://blogs.gnome.org/ignatenko/2015/07/31/
#
how-to-set-up-network-audio-server-based-on-pulseaudio-and-auto-discovered-via-avahi/
#
https://wiki.openlab-augsburg.de/Musikanlage
#
https://askubuntu.com/questions/895376/pulseaudio-streaming-over-network-not-working
```

# device setting for orange-pi-zero

- add extention board

- prepare backup


```bash
sudo cp /boot/armbianEnv.txt /boot/armbianEnv_org_install.txt
```

. add necessary overlays and spi bus parameter

```bash
cat << EOF | sudo tee -a /boot/armbianEnv.txt
overlays=analog-codec usbhost2 usbhost3 spi-spidev
param_spidev_spi_bus=0
EOF

# reboot the sbc
sudo shutdown -Fr now
```


## install

```bash
# debian
sudo apt-get install -y pulseaudio-module-zeroconf pulseaudio  avahi-daemon alsa-utils
# add to config
echo "load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1;192.168.0.0/24 auth-anonymous=1" |sudo tee -a  /etc/pulse/system.pa
echo " load-module module-zeroconf-publish" |sudo tee -a  /etc/pulse/system.pa  

# user for pulseaudio
useradd -m -G audio media -s /bin/bash
# switch to user
sudo su - media
# set env for D-BUS
# Failed to connect to bus: No such file or directory
export XDG_RUNTIME_DIR=/run/user/$(id -u)
# set permanne
# enable
systemctl --user enable pulseaudio.service
# restart pulseaudio
systemctl --user restart pulseaudio || pkill pulseaudio
# restart pulseaudio
systemctl --user satus pulseaudio 
# set 
loginctl enable-linger media
# 

# status
ps -C pulseaudio

```

## control loudspeaker volumes

```bash
# max volumes
/usr/bin/amixer set 'Line Out' 100% unmutess
# NEVER use 100% of any technical device :-)
# better
# max volumes
/usr/bin/amixer set 'Line Out' 90% unmute
```

## pactl - Control a running PulseAudio sound server

- doku 

```bash
man pactl
https://wiki.ubuntuusers.de/PulseAudio/
```

- info

```bash
pactl info
```
- list of devices - sinks

```bash
pactl list
```

# https://wiki.archlinux.org/index.php/PulseAudio/Examples#Set_default_input_source
# https://ubuntuforums.org/showthread.php?p=4928900

```bash
alsamixer -Dhw
```

## test local sound

```bash
aplay -D plughw:0,0 /usr/share/sounds/alsa/Front_Center.wav
```



sudo fuser -v /dev/dsp* /dev/snd/*

aplay -l # list of device

~/.config/pulse

## debug pulseaudio

```bash
pulseaudio -k && sleep 3 && pulseaudio -vv
```

## stop pulseaudio

```bash
pulseaudio -k
```