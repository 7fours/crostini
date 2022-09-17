# crostini
Google's linux container Crostini on ChromeOS. Basically anything related to that.

Dev machine is a 2017 Pixelbook i5, 8gb ram. 

Ubuntu on Crostini container
No complete automated install processs due to no scripts from crosh terminal

Installation process:

crosh terminal in google chrome (ctrl+alt+t):
```
vmc start termina
```
localhost@termina:` lxc list `

This should return a list of containers. Assuming you have done no previous modifications, this should be your debian container named penguin.

```
lxc stop penguin --force
```
```
lxc rename penguin debian 
```
This will free up the name penguin for your new ubuntu container. This is important (as far as im aware) to allow chrome os' file system to integrate into linux's.
```
lxc launch ubuntu:21.10 penguin 
```
This will create a new container named penguin and download/install the Ubuntu 21.10 image onto it.
```
lxc exec penguin -- bash
```
root user ubuntu in container:
```
apt update && apt upgrade
```
```
echo "deb https://storage.googleapis.com/cros-packages bullseye main" > /etc/apt/sources.list.d/cros.list
```
```
if [ -f /dev/.cros_milestone ]; then sudo sed -i "s?packages?packages/$(cat /dev/.cros_milestone)?" /etc/apt/sources.list.d/cros.list; fi
```
```
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1397BC53640DB551
```
```
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 7638D0442B90D010
```
```
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 04EE7237B7D453EC
```
```
apt update
```
In order for proper file's integration, we need to install Google's Crostini packages, but a workaround is required because apt does not like to install it directly for some reason.
After adding the keys to ubuntu, we will create the actual package.
```
apt install binutils
```
```
apt download cros-ui-config 
```
ignore the warning messages

Creating the Crostini packages..
```
ar x cros-ui-config_0.12_all.deb data.tar.gz
```
```
gunzip data.tar.gz
```
```
tar f data.tar --delete ./etc/gtk-3.0/settings.ini
```
```
gzip data.tar
```
```
ar r cros-ui_config_0.12_all.deb data.tar.gz
```
Now the Crostini packages are ready to be installed
```
apt install cros-guest-tools ./cros-ui-config_0.12_all.deb
```
```
rm cros-ui-config_0.12_all.deb
```
```
rm -rf data.tar.gz
```
We need the adwaita-icon-theme-full package, otherwise linux apps will have a tiny cursor
```
apt install adwaita-icon-theme-full
```
Now just shut down the container and then restart by opening the terminal. ```#Often times, you'll have to reopen the terminal app multiple times before it starts up right after a container restart.```
```
shutdown -h now
```
Next, we have to update from Ubuntu 18.04 LTS to Ubuntu 21.10..
```
sudo do-release-upgrade
```
	Wait a few hours to upgrade..
This should upgrade you to Ubuntu 20.04 LTS
```
sudo nano /etc/update-manager/release-upgrades
```
Edit the ```prompt=lts``` section to ```prompt=normal```
```
sudo do-release-upgrade
```
This should switch you from the LTS line to the normal line and install Ubuntu 21.04. ```I have tried many different orders and slightly different upgrade methods and it always seems to upgrade in this weird path instead of going directly to 21.10 but I promise it is possible.```

	I could be wrong, but based on the few nanosecond glance at the output when attempting this, if you try to make the container with the 20.04/21.04/21.10 images (despite them being provided,) the cros-ui-config packages error at install because your os is too new.

	Wait the few more hours to upgrade again....

	I'm only just realizing this is such a long process that must constantly be babysat for input that I'm not sure it's worth it.. but we move.
```
sudo do-release-upgrade
```
