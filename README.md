# MagicMirror1

# RaspberryPi

## Install lts Nodejs

https://pimylifeup.com/raspberry-pi-nodejs/

```bash
# check default lts version
apt show nodejs

sudo apt install -y ca-certificates curl gnupg
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/nodesource.gpg
NODE_MAJOR=22 # lts version https://nodejs.org/en
echo "deb [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
sudo apt update
# verify lts
apt show nodejs # should show new version

# install 
sudo apt install nodejs

# verify version
node -v
```

Installing Additional Development Tools

```bash
sudo apt install build-essential
 ```

## MagicMirror

https://docs.magicmirror.builders/getting-started/installation.html#manual-installation

### Installation

```bash
git clone https://github.com/MagicMirrorOrg/MagicMirror
cd MagicMirror/
npm run install-mm
cp config/config.js.sample config/config.js
npm run start
```

### Update

https://docs.magicmirror.builders/getting-started/upgrade-guide.html

Note: Backup `config.js`

```bash
git pull && npm run install-mm
```

### Autostart

https://docs.magicmirror.builders/configuration/autostart.html

Install PM2

```bash
sudo npm install -g pm2
pm2 startup
```
Setup magic mirror startup script

```bash
cd ~
nano mm.sh

```
Add following

```bash
cd ./MagicMirror
DISPLAY=:0 npm start
```

add execution permissions

```bash
chmod +x mm.sh
```

Start the script

```bash
pm2 start mm.sh --watch
```

Save PM2 

```bash
pm2 save
```

# Autologin as pi desktop

```bash
sudo raspi-config
```

Choose option: 1 System Options
Choose option: S5 Boot / Auto Login
Choose option: B4 Desktop Autologin automatically logged in as user
Select Finish, and reboot the Raspberry Pi.

## Update PI

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install rpi-update
sudo rpi-update
sudo apt-get autoremove -y
sudo apt-get clean
sudo reboot
```

## Hide mouse

```bash
sudo sed -i -- "s/#xserver-command=X/xserver-command=X -nocursor/" /etc/lightdm/lightdm.conf
```

## Keep the lights on

```bash
sudo nano /etc/lightdm/lightdm.conf
```

```bash
[Seat:*]
# don't sleep the screen
xserver-command=X -s 0 dpms
```

## Stop swap

```bash
sudo service dphys-swapfile stop
```

## Setting up status overview when logging in

```bash
nano ~/.bashrc
```
Append

```bash
# Locale settings to avoid warnings
export LANG=en_GB.UTF-8
export LANGUAGE=en_GB.UTF-8
export LC_ALL=en_GB.UTF-8

# MOTD 
let upSeconds="$(/usr/bin/cut -d. -f1 /proc/uptime)"
let secs=$((${upSeconds}%60))
let mins=$((${upSeconds}/60%60))
let hours=$((${upSeconds}/3600%24))
let days=$((${upSeconds}/86400))
UPTIME=`printf "%d days, %02dh%02dm%02ds" "$days" "$hours" "$mins" "$secs"`

# get the load averages 
read one five fifteen rest < /proc/loadavg

DISK_USAGE=$(df -h / | grep -E '/' | awk '{print $3" used of "$2}')

echo "$(tput setaf 2)
   .~~.   .~~.    $(date +"%A, %e %B %Y, %r")
  '. \ ' ' / .'   $(uname -srmo)$(tput setaf 1)
   .~ .~~~..~.
  : .~.'~'.~. :   Uptime.............: ${UPTIME}
 ~ (   ) (   ) ~  Memory.............: $(awk '/MemFree/ {print $2}' /proc/meminfo)kB (Free) / $(awk '/MemTotal/ {print $2}' /proc/meminfo)kB (Total)
( : '~'.~.'~' : ) Load Averages......: ${one}, ${five}, ${fifteen} (1, 5, 15 min)
 ~ .~ (   ) ~. ~  Running Processes..: $(ps ax | wc -l | tr -d " ")
  (  : '~' :  )   IP Addresses.......: $(hostname -I | awk '{print $1}')
   '~ .~~~. ~'    Free Disk Space SD.: ${DISK_USAGE}
       '~'        CPU Temperature....: $(vcgencmd measure_temp | cut -c "6-9") C
          
$(tput sgr0)"

```

## Set Wallpaper

Copy wallpaper via ssh

```bash
scp wallpaper.jpg pi@172.28.34.152:~
```

Set wallpaper

```bash
sudo apt-get install feh
DISPLAY=:0 feh --bg-scale ~/wallpaper.jpg
```

## Troubleshooting

-bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)

```
sudo nano /etc/environment
```

then add

```sh
LANG=en_GB.UTF-8
LANGUAGE=en_GB.UTF-8
LC_ALL=en_GB.UTF-8
```

## Auto login not working

https://forums.raspberrypi.com/viewtopic.php?t=340632

```
sudo nano /etc/lightdm/lightdm.conf
```

comment out incorrect values, e.g.: 

```bash
user-session=LXDE-pi-x
autologin-session=LXDE-pi-x
```
