# Configuring server tools and others

[TOC]

## 1- SSH

### Create an SSH key

I already have the OS installed in all of the device.

Now I want to setup the web server.

<aside>
📎 To enable SSH on a device you must run:
**systemctl start sshd**

</aside>

## 2.1 Setting the web server device

I will first try to host a public website using a private DNS of my choice.

I already have a DNS:

guigoch.cloudns.org

### 2.1.1 Setting up WOL on this device

I’ve tried to use WOL in the command terminal.

To do so i have downloaded wakeonlan package

```bash
sudo apt install etherwake
```

Then scan network using nmap

```bash
sudo nmap 192.168.1.*
```

Here I get all the ip and MAC addresses of the network what I’m looking for here is the MAC address of the devices I want to WOL:

I can see the MAC address of the server: 

```bash
Nmap scan report for 192.168.1.128
Host is up (0.0071s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 00:24:81:D9:B3:CC (Hewlett Packard)
```

So to WOL this device I only need to do so:

```bash
wakeonlan 00:24:81:d9:b3:cc

PC-CASA: 
wakeonlan 18:C0:4D:53:97:BB
```

<aside>
📎 Important!
To use WOL it must be enabled on the BIOS of the device, if not it will not be able to recieve the magic packet

</aside>

### 2.1.2 Disable poweroff automatically

I’ve noticed that the device powers off automatically. 

I want to prevent this.

https://www.dell.com/support/kbdoc/en-us/000179566/how-to-disable-sleep-and-configure-lid-power-settings-for-ubuntu-or-red-hat-enterprise-linux-7

```bash
systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

After authentication

```bash
sudo nano /etc/systemd/logind.conf
```

And modify line to:

```bash
HandleLidSwitch=ignore
```

After restart the service to apply changes

```bash
systemctl restart systemd-logind
```

And it should be done

### 2.1.5 Setting up protection for the SSH server

Notes on security for ssh

https://help.ubuntu.com/community/SSH/OpenSSH/Configuring

Teacher also said I could use fail2ban to detect intrusion and create a “prison” for ip’s.

To track the log of ssh, we can use this command: 

```bash
journalctl -u shh #for logs
#OR
journalctl -fu ssh #for realtime log
```

fail2ban

https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-20-04






### Create SSH tunnel

Creating an ssh tunnel I can access a service that is running on a certain port elsewhere.

~~~ bash
ssh -L <localport>:<host on remote network>:<remote port> <ssh server>

ssh -L 8000:192.168.1.136:8000 guigoch.ddns.net
~~~

## 2- VPN

TODO: Setup a VPN server

## 3- WAKE ON LAN

Installing wakeonlan to allow to wake computers using their MAX ADDRESS that are connected to the local network.

TODO: Missing info on wakeonlan

## 4- Disable power off

To disable automatic power off, do this:

~~~ bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
~~~

Then reboot system and check if it has been saved doing so:

~~~ bash
sudo systemctl status sleep.target suspend.target hibernate.target hybrid-sleep.target
~~~

If I ever want to reenable for some reason this is it:

~~~ bash
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
~~~

To suspend the system from going to sleep on lid closed  edit the following file '/etc/systemd/logind.conf'

~~~ bash
sudo nvim /etc/systemd/logind.conf
~~~

And append the following lines

~~~ bash
[Login] 
HandleLidSwitch=ignore 
HandleLidSwitchDocked=ignore
~~~

Save and exit. Then reboot so that it makes effect


## 5- Creating a media file server

Using nextcloud, an opensource project

Create a ssh tunnel

```csharp
ssh -L 8080:192.168.1.134:8080 [guigoch@192.168.1.134](mailto:guigoch@192.168.1.134)
```

access AOI portal using this url:

[https://guigoch.ddns.net:8080](https://guigoch.ddns.net:8080/)

or this one [https://192.168.1.134:8080](https://192.168.1.134:8080/)

## 6- Installing Neovim on all pcs

To be able to efficiently edit files and other stuff

~~~ bash
#Ubuntu
sudo apt install neovim

#arch
yay -S neovim-git
~~~

I also added user permission from the server user to be able to edit files without needing sudo

Now time to configure it.

1- Install font

    curl -OL https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip

Unzip file

    unzip JetBrainsMono.zip -d JetBrainsMono

Create font directory

    mkdir -p ~/.local/share/fonts

Move these two font binaries to the destination directory:
    
    mv ./JetBrainsMono/JetBrainsMonoNLNerdFont-Regular.ttf ~/.local/share/fonts
    mv ./JetBrainsMono/JetBrainsMonoNLNerdFont-SemiBold.ttf ~/.local/share/fonts

Clear and regenerate your font cache with the following command:

    fc-cache -f -v

2- Configure terminal to use font

I dont know how to do it

3- Install NvChad

Transitioning from Vim                          nvim-from-vim

1. To start the transition, create your init.vim (user config) file:

    :call mkdir(stdpath('config'), 'p')
    :exe 'edit '.stdpath('config').'/init.vim'

2. Add these contents to the file:

    set runtimepath^=~/.vim runtimepath+=~/.vim/after
    let &packpath = &runtimepath
    source ~/.vimrc

3. Restart Nvim, your existing Vim config will be loaded.

git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1

## 7- User permissions

When logging through ssh and wanting to use Neovim to edit any file of the project I had to always use sudo
It is kind of tiring and inefficient.

So I have added my  user 'guigoch' as a file owner or directory owner using

~~~ bash
sudo chown guigoch *
~~~

I also had to change read and write permissions using

~~~ bash
sudo chmod 777 * 
~~~
