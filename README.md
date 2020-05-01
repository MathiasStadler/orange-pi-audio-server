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

## install

```bash
# debian
sudo apt-get install pulseaudio-module-zeroconf pulseaudio  avahi-daemon
# add to config
echo "load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1;192.168.0.0/24" |sudo tee -a  /etc/pulse/system.pa
echo " load-module module-zeroconf-publish" |sudo tee -a  /etc/pulse/system.pa  
# restart pulseaudio
systemctl --user restart pulseaudio || pkill pulseaudio
# status


```