# Workstation (Gnome)

[Make it useful](#make-it-useful)  
[Install favorite packages & media codecs](#install-favorite-packages)  
[Make it yours](#make-it-yours)  
[Extensions](#extensions)  
[Themes](#themes)  
[--Pantheon--](#pantheon)  

### Make it useful:

#### enable the rpm-fusion repos #for multimedia, etc.
    su -c 'dnf -y install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm'

#### Install favorite packages
    su -c 'dnf -y install nano inkscape gnome-mpv gnome-tweak-tool chrome-gnome-shell paper-icon-theme adapta-gtk-theme breeze-cursor-theme pop-icon-theme tilix-nautilus pop-gtk-theme gthumb libdvdcss chromium freshplayerplugin'

#### Install Media Codecs
    su -c 'dnf groupupdate Multimedia'

#### Start & enable ssh server
    systemctl start sshd
    systemctl enable sshd

[manage repos](https://fedoramagazine.org/configure-software-repositories-fedora/)

### Make it yours:

- [**Extensions**](https://extensions.gnome.org)
	-   [open weather](https://extensions.gnome.org/extension/750/openweather/)
	-   [lock keys](https://extensions.gnome.org/extension/36/lock-keys/)
	-   [dash to panel](https://extensions.gnome.org/extension/1160/dash-to-panel/) / [dash to dock](https://extensions.gnome.org/extension/307/dash-to-dock/)

#### Themes
**operation on theme folder:**

    sudo cp -a /source/folder /dest/

**ex:**

    sudo cp -a '/home/cyril/installers/Gnome-OSX-II-2-6-NT' '/usr/share/themes/Gnome-OSX-II-2-6-NT'

### Issues?

If upgrade to newer Fedora release, check repositories referencing older releases:

[https://fedoramagazine.org/configure-software-repositories-fedora/](https://fedoramagazine.org/configure-software-repositories-fedora/)

-   Cutter software? :: [ref_01](http://libregraphicsworld.org/blog/entry/vinyl-cutting-on-linux-the-real-deal)
    
-   [inkcut](http://inkcut.sourceforge.net) (inkscape extension)
    
#### system boots, but not to gdm/login? [reboot to prompt, & disable wayland](https://ask.fedoraproject.org/en/question/70961/fedora-22-will-not-boot-after-installation-vm/?answer=77048#post-id-77048)

1.  At the grub boot menu, press 'e' to edit the bootup settings.
2.  go down to the line starting with "linux16 /vmlinuz" (it will probably span multiple lines) and add a " 3" to the end (that is a space, then a '3'). This will tell the bootup scripts to load only the text console, and not attempt to start a graphical login screen.
3.  Press CTRL-X to boot the edited settings.
4.  login as root.
5.  "vi /etc/gdm/custom.conf" (or use your editor preference instead of vi), and delete the leading "#" from the line "#WaylandEnable=false". Save the file.
6.  Reboot and enjoy :)
Note that I have heard that replacing step 5 with "dnf update" will also fix the issue (and update the rest of the system in the process). This would be preferable to just updating the file as per the above, but you can always follow my instructions and then run an update once you are able to graphically login.

# Android 7.1 compile environment (F25)

[(originally posted to xda)](https://forum.xda-developers.com/chef-central/android/guide-setting-android-compile-t3530696)

(package list is adapted from [Omni’s instructions for F19](https://docs.omnirom.org/Setting_Up_A_Compile_Environment#Fedora_19_x64), to compile Android 7.1 on F25)

**From a clean installation of Fedora-25:**

Update the installation

    su -c 'dnf -y update'

Install additional packages that we need to compile AOSP

    su -c 'dnf -y install zip curl gcc gcc-c++ flex bison gperf glibc-devel.{x86_64,i686} zlib-devel.{x86_64,i686} ncurses-devel.i686 libX11-devel.i686 libstdc++.i686 readline-devel.i686 libXrender.i686 libXrandr.i686 perl-Digest-MD5-File python-markdown mesa-libGL-devel.i686 git schedtool pngcrush [ncurses-compat-libs](https://ask.fedoraproject.org/en/question/89534/parrallel-install-ncurses-6-ncurses-5-on-fedora-24/)  [java-1.8.0-openjdk-devel](http://stackoverflow.com/questions/5407703/javac-command-not-found) xz-lzma-compat android-tools'

install & configure repo

    mkdir -p ~/bin
    curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    chmod a+x ~/bin/repo
add line to end of ~/.bashrc

    export PATH=~/bin:$PATH
configure git to identify you

    git config --global user.email "your@email.address"
    git config --global user.name "Your Name"

[setting up ccache](https://source.android.com/source/initializing.html#setting-up-ccache) (for quicker rebuilds):

Issue these commands in the root of the source tree:

    export USE_CCACHE=1
    export CCACHE_DIR=/<path_of_your_choice>/.ccache
prebuilts/misc/linux-x86/ccache/ccache -M 50G
The suggested cache size is 50-100G.
The ccache size is stored in the CCACHE_DIR and is persistent.
For persistence, add `USE_CCHACHE’ snd ‘CCACHE_DIR` commands to .bashrc (or equivalent):

Watch ccache being used:
    watch -n1 -d prebuilts/misc/linux-x86/ccache/ccache -s

# Pantheon
**Workstation:Pantheon@Fedora ← working, limited selection of usable icons**  

1.  install Fedora gnome (for preferred apps & full gtk3-workstation functionality)
2.  install pantheon session (& few extras) but not elementary-apps
3.  swap lightdm for gdm
4.  tweak & profit
   
**Once gnome workstation installed:**

    dnf copr enable decathorpe/elementary-nightly
    dnf in pantheon-session-settings switchboard elementary-*theme htop lightdm-gtk-greeter-settings

    systemctl disable gdm
    systemctl enable lightdm

**apply preferences system-wide:**  
/usr/share/glib-2.0/schemas/  
[global customization via overrides](http://www.techytalk.info/customize-default-desktop-environment-settings-gnome-centric-linux-distributions/)  

60_fedora.pantheon.gschema.override  
```
#/usr/share/glib-2.0/schemas/60_fedora.pantheon.gschema.override

[io.elementary.files.preferences]
single-click=false

[org.pantheon.desktop.gala.appearance]

[org.pantheon.desktop.gala.behavior]
overlay-action='wingpanel --toggle-indicator=app-launcher'

[org.gnome.desktop.wm.preferences]
button-layout='menu:close'
mouse-button-modifier='<Super>'
resize-with-right-button=true
theme='elementary'
titlebar-font='Open Sans Bold 9'
titlebar-uses-system-font=false

[org.gnome.desktop.interface]
cursor-theme='elementary'
gtk-theme='elementary'
icon-theme='elementary'
#icon-theme='ePapirus'
document-font-name='Open Sans 10'
font-name='Open Sans 9'
monospace-font-name='Roboto Mono 10'
show-unicode-menu=false
toolbar-style='icons'

[org.gnome.desktop.peripherals.touchpad]
tap-to-click=true

[org.gnome.Terminal.Legacy.Settings]
menu-accelerator-enabled=false
default-show-menubar=false
```
**Cleanup…**  

    rm -r ~/.config/dconf
    reboot

**Decathorpe recommended installation:**

    install gnome workstation
    dnf copr enable decathorpe/elementary-nightly

    dnf in pantheon-desktop-settings switchboard lightdm-gtk-greeter-settings elementary-icon-theme elementary-sound-theme elementary-theme*

    systemctl disable gdm
    systemctl enable lightdm

apply gschema override file
delete .config/dconf  

reboot

extras?  
[https://github.com/btd1337/urutau-icons](https://github.com/btd1337/urutau-icons)  
[https://github.com/btd1337/Newaita](https://github.com/btd1337/Newaita)
