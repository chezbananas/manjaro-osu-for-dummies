# manjaro-osu-for-dummies
beginner's guide to low-latency audio on osu! with manjaro KDE

CREDITS BELOW

linux is all about choice and flexibility or something - you can use any distro and all the stuff will work right? unfortunately i am an idiot so i will be the antithesis to that philosophy and give a tutorial for only one distro (manjaro kde)

# step 1: install manjaro kde

## follow [this](https://itsfoss.com/install-manjaro-linux/) guide, this is not a manjaro install guide but i left a few notes for each step below

- FOR LAPTOP USERS: disable bitlocker if you’re using it
- then disable secure boot in your UEFI/BIOS, if you don’t know how to do this basically spam F2/Del on your bootup before you get to the scary looking screen, then look for secure boot and disable that
- step 1: i used KDE, this shouldn’t affect much but KDE is pretty similar to windows so it’s very comfortable
- step 2: i prefer [rufus](https://rufus.ie/en), but use whatever works
  - make sure you change your boot order in your bios to let your computer boot from USB instead of windows
- step 3: on newer versions of manjaro, it’ll give you two options: boot with open-source drivers, or boot with proprietary drivers
  -boot with PROPRIETARY drivers for better performance
- step 4: if you shrunk your partitions in windows properly, you should see “install alongside”
  -use this and click on the free space, and manjaro will handle the rest
     - if you don't see 'install alongside': i would recommend going back to windows and trying to fix your partitions again, or you can try setting up your partitions manually through the manjaro installer if you’re comfortable with that (other guides explain how to do this)
# step 2: housekeeping after a fresh install
- if after you restart your computer you keep booting to the manjaro installer, turn off your computer, remove the USB stick, and then start it again - you should boot to manjaro now
after installing manjaro, make sure you’re connected to your network
- look in the bottom right - you probably need to update software
- click through that and install updates, should be pretty straightforward
- reboot if prompted to
- after you do that, we’re ready to install pipewire and osu related stuff
# step 3: pipewire and regular wine
- open a terminal (on manjaro KDE you can use ctrl+alt+t)
- copy+paste this (you can use ctrl+shift+v to paste, right click and select paste from the context menu, or middle click in the terminal)
```
sudo pacman -S giflib lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo libxcomposite lib32-libxcomposite libxinerama lib32-libxinerama ncurses lib32-ncurses opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader cups samba dosbox git p7zip wget
```
  - you’ll be prompted for your password - type it in
- type y whenever prompted to

## installing wine

- on manjaro you can use “add/remove software” - simply search for wine and install that
- on arch you enable multilib support by editing /etc/pacman.conf, then just sudo pacman -S wine (but if you use arch why are you reading this)

## installing pipewire

- in terminal:
  ```
  sudo pacman -Rdd pulseaudio
  sudo pacman -S pipewire pipewire-pulse pipewire-jack pipewire-alsa
  ```
- then reboot
### IF YOU WANT ABSOLUTE LOWEST LATENCY
- i do not use this - gives me audio stability issues, and you will already 100% notice the difference without this patch
- open terminal
```
mkdir -p ~/.config/pipewire && cp -rv /usr/share/pipewire/* ~/.config/pipewire/
sudo nano ~/.config/pipewire/pipewire-pulse.conf
```
- scroll down until you see context.modules, then scroll down until you see #pulse.min.req
  - delete the # before pulse.min.req, then change the 256 in 256/48000 to a 32
- do the same with #pulse.min.quantum
  - delete the # before pulse.min.quantum, then change the 256 in 256/48000 to a 32
- save with ctrl+O and then exit with ctrl+x
- then reboot
# step 4: installing osu
[this](https://osu.ppy.sh/community/forums/topics/794952?n=1) post is my savior
- terminal:
```
git clone https://gitlab.com/osu-wine/osu-wine
cd osu-wine
sudo ./install.sh
```
- hit yes at any prompts
```
osu-wine
```

- it’s common to have some errors at this point - just click “no” and eventually osu should run

- after osu is working we’ll have to go do the low latency stuff

## IF YOU HAVE ISSUES WITH 144/240hz (especially with multiple displays) - disable desktop compositing
- manjaro: search “compositor," which should bring you to system settings
  - then disable compositor on startup

# step 5: poon’s wine
## option 1: guaranteed working as of 8/31/21
- join poon’s [discord](https://discord.com/invite/thepoon) they are also very helpful if you have questions about installing osu, go to #osu-linux and ask em there)
  - alternatively you can go to [this](https://drive.google.com/drive/folders/17MVlyXixv7uS3JW4B-H8oS4qgLn7eBw5) folder but it’s not guaranteed to be updated + you should not trust me this much
- go to #osu-linux and pinned messages, then download the latest wine-osu they have (as of now, it’s 6.14)

- open a terminal:
```
cd Downloads
sudo pacman -U wine-osu-6.14-1-x86_64.tar.zst
```
- change wine-osu-6.14… .tar.zxt for whatever the name of the file you downloaded is (the one above should be right though)

## option 2: should work but i haven’t tested it
- open add/remove software
- click the top right hamburger menu (three horizontal lines)
- go to preferences
- go to third party
- enable aur support
- exit preferences
- search for wine-osu
- install that

# step 6: putting it all together
- open a terminal
```
cp /etc/osu-wine.conf ~/.osu-wine.conf
nano ~/.osu-wine.conf
```
  - **DO NOT EDIT** ~/osu-wine/.osu-wine.conf, this does not work and you will spend a day ripping your hair out trying to figure out why your latency is so high
- scroll down to “PooN’s wine-osu variables”
- remove the # before PATH=”/opt/wine-osu/bin:$PATH”
  - if you want you can remove the # before export STAGING…, but it’ll work without it (i did not remove it, once again for stability purposes)
- ctrl O then enter to save, ctrl X to exit
- type osu-wine in a terminal to start osu, and you should be good to go!

## if you have no audio: 
- if you uncommented export STAGING… use
```nano ~/.osu-wine.conf```
again and add the # before export again
- try enabling and disabling audio compatibility mode - if audio works after this and is stable, then you’ll just have to do this every time you start osu

## if those did not help:
- terminal:

```nano ~/.osu-wine.conf```

- uncomment export STAGING_AUDIO_DURATION and set it to 100000 (one hundred thousand)
- if it works now, decrease until it’s unstable, then go back to your last stable setting
- if it doesn’t work, increase until it’s stable

### “i can’t tell if it’s working or not”
- if you can’t tell if it’s working, it’s probably not working
- go back through the instructions again - namely the editing ~/.osu-wine.conf NOT ~/osu-wine/osu-wine.conf
- also side note a good map to test it is deviouspanda’s piano x forte, and bubbleman’s skin has good low-latency hitsounds

# step 7: sharing an osu-install between windows and linux
- these instructions are only for KDE, idk how it works on other DEs but it’s probably pretty similar
- google how to setup symlinks if you want to do this via the terminal

## IMPORTANT
- when you restart and immediately run osu-wine, your drive/partition might not be mounted on boot (the symlink won’t work)
- [this](https://www.youtube.com/watch?v=ceQZw-Ot9r8) guide is excellent
- open “disks”  application
  - if you don’t have this - use “add/remove software” and install “GNOME Disks”
- go to your drive with the old osu install
- go to the partition with the old osu install
- click “edit mount options”
- disable “user session defaults”
- enable “mount at system startup” and “show in user interface”
- restart your computer
  - do this step (setting up automount) first, because sometimes this can mess with drive labels and you would have to set your symlinks up again if you did this second

## setting up symlinks
- in your file manager, navigate to your OLD osu folder, then to your NEW osu folder (probably (your home directory)/.local/share/osu-wine/OSU)
- IF YOU CAN’T FIND .local - enable hidden files with the hamburger menu in the top right, then click “show hidden files”
  - alternatively you can use ctrl H
- drag the folders/files you want to share from your old folder to your new folder, and when the context menu pops up, click “link here”
  - common ones: skins, screenshots, songs, collection.db, osu!.cfg, osu!.db, osu!(your username).cfg, scores.db
- test by running osu-wine in a terminal - if done properly, you should have your old skins/songs/collections, and you shouldn’t have to recalculate difficulties or anything

# step 8: opentabletdriver
- [this](https://osu.ppy.sh/community/forums/topics/1340468?n=1) guide is once again Pretty Good
- terminal:

```sudo pacman -S dotnet-runtime dotnet-sdk```

- open add/remove software
### if you haven’t enabled AUR support, do this:
- click the top right hamburger menu (three horizontal lines)
- go to preferences
- go to third party
- enable aur support
- exit preferences

- search for and install opentabletdriver
  - **DO NOT INSTALL** opentabletdriver-git, plugins (smoothing) will not work

- terminal:
```
systemctl --user daemon-reload
systemctl --user enable opentabletdriver --now
```
## if OTD works (unlikely), then you’re good; otherwise, in terminal:
run these individually:
```
git clone https://github.com/InfinityGhost/OpenTabletDriver-udev.git
cd OpenTabletDriver-udev
git submodule update --init --remote
./build.sh
sudo mv ./build/99-opentabletdriver.rules /etc/udev/rules.d/99-opentabletdriver.rules
sudo udevadm control --reload-rules
cd ..
rm -rf OpenTabletDriver
rm -rf OpenTabletDriver-udev
```
- restart computer
- hit the windows/super key and type opentabletdriver, then you can configure your area/settings

### if you have issues with a “ghost cursor” look at the [OTD wiki](https://github.com/OpenTabletDriver/OpenTabletDriver/wiki/Linux-FAQ)
- open your file explorer and go to home
- right click → create new → text file
  - call it whatever you want, but make sure it ends in sh (i’ll use tablet.sh)
- you can delete this afterwards so it doesn’t matter
- open tablet.sh (it shouldn’t execute, but if it does, right click and open with KATE)
- put this in there:
```
echo "blacklist wacom" | sudo tee -a /etc/modprobe.d/blacklist.conf
sudo rmmod wacom
```
- save it
- open terminal:
```
cd
chmod +x tablet.sh
./tablet.sh
```
- you should see “blacklist wacom” in the terminal, and your cursor should stop ghosting!

# step 9 gosumemory+josu-trainer

- download [gosumemory](https://github.com/l3lackShark/gosumemory/releases/tag/1.3.3)
  - download linux amd64 version (assuming you're on 64 bit, otherwise 386)
- extract the zip to wherever you'd like to have gosumemory installed (I have it installed in /home/gosumemory)
- open terminal:
```
cd (your gosumemory directory), for me I type cd gosumemory
sudo chmod +x gosumemory
sudo ./gosumemory -path /(YOUR PATH TO YOUR SONGS FOLDER)
```
  - this should be /home/(YOUR COMPUTER'S NAME)/.local/share/osu-wine/OSU/Songs
    - alternatively you can navigate to your songs folder in your file explorer, then right click on songs and click "copy location"
  - remember that you can paste this with ctrl+shift+v

- open a new terminal tab and open osu (osu-wine in terminal)  
- verify that gosumemory isn't spitting out errors

- to not have to copy paste your path to songs directory every time:
  - close gosumemory if it's running
  - open your file explorer and navigate to your gosumemory directory
  - open config.ini with KATE (or any other text editor)
    - on line 3 you should see path = auto; replace auto with the path to your songs directory
      - example: path = /home/(my computer's name)/.local/share/osu-wine/OSU/Songs
  - verify that gosumemory is working by opening a terminal and:
    - cd into the gosumemory directory
```
sudo ./gosumemory
```

- if you set it up properly, then it should detect your songs folder (look at the console's output), otherwise double-check that everything is right

- in the future, to run gosumemory, open a terminal, cd into your gosumemory directory, and then `sudo ./gosumemory`

## josu-trainer

### i'm only going to show you the simple idiot way with precompiled binaries, but it might not always be up to date; join poon's discord and look at the pinned messages in #osu-linux for a guide on how to build josu-trainer from scratch

- download josu-trainer from [this link](https://www.dropbox.com/s/gpo9sibpo37u5z3/josu.zip?dl=1)
- extract this into wherever you want josu-trainer to be (for me, i'll use /home/josu)
- open your file manager and navigate to wherever you extracted josu-trainer (once again, for me /home/josu) 
  - open the "bin" folder
  - create a new file called "josutrainer-config.properties"
    - edit that file with KATE, and copy paste the following
```
	ffmpeg=/usr/bin/ffmpeg
	generate_empty_osz=true
	osz_directory= (PATH TO YOUR SONGS FOLDER), e.g. /home/(my computer's name)/.local/share/osu-wine/OSU/Songs
```
- save and quit
- open a terminal:
```
cd josu (or wherever you extracted josu-trainer to)
cd bin
./josu-trainer
```
- and josu-trainer should be up and running! note that you need gosumemory and osu! running as well for it to detect maps properly

**IMPORTANT**
when using josu-trainer, make sure you rename your difficulties manually **IN** josu-trainer before generating them
- not doing so will corrupt your map

# Conclusion
everything should be working at this point, if it doesn’t then look through these three forum posts for additional troubleshooting steps:

also this is a bibliography: 

marshnello guide: https://osu.ppy.sh/community/forums/topics/1248084

osu-wine by forefront: https://osu.ppy.sh/community/forums/topics/794952

OTD guide by iodine: https://osu.ppy.sh/community/forums/topics/1340468  

guide on auto mounting drives: https://www.youtube.com/watch?v=ceQZw-Ot9r8

if it’s still broken then if i know you feel free to ping me and i’ll see what i can do but idk wtf i’m doing tbh i just know this worked for me on 3 different machines

