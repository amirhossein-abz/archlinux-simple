# Arch Linux Installation

#### Internet 

```sh
ip a
ping archlinux.org
```
---
<br>

#### Time

```sh
timedatectl set-ntp true
```

---
<br>

#### Disks

```sh
fdisk /dev/sda
```
- `Command (m for help):` `g`  &nbsp;&nbsp;&nbsp;&nbsp; Use This For GPT

- ` Command (m for help): ` `n` &nbsp;&nbsp;&nbsp;&nbsp; Create new Partition

- `Select (defualt p): `  `p` &nbsp;&nbsp;&nbsp;&nbsp;  For Primary Partition

- `Partition number(1-128,defualt 1): ` `default` &nbsp;&nbsp;&nbsp;&nbsp; Choose Defualt 

- `First Sector :` `default` &nbsp;&nbsp;&nbsp;&nbsp; Choose Default 

- `Last sector, +/-sectors or +/-size{K,M,G,T,P} : ` `+500M`&nbsp;&nbsp;&nbsp;&nbsp; 500 Mib For UEFI
<br><br>
- `Command (m for help):` `n` &nbsp;&nbsp;&nbsp;&nbsp; Create new Partition

- `Select (defualt p): `  `p` &nbsp;&nbsp;&nbsp;&nbsp;  For Primary Partition

- `Partition number(1-128,defualt 2): ` `default` &nbsp;&nbsp;&nbsp;&nbsp; Choose Defualt 

- `First Sector :` `default` &nbsp;&nbsp;&nbsp;&nbsp; Choose Default 

- `Last sector, +/-sectors or +/-size{K,M,G,T,P} : ` `+50G`&nbsp;&nbsp;&nbsp;&nbsp;  50-200 GB For /root
<br><br>
- `Command (m for help):` `n` &nbsp;&nbsp;&nbsp;&nbsp; Create new Partition
- `Select (defualt p): `  `p` &nbsp;&nbsp;&nbsp;&nbsp;  For Primary Partition

- `Partition number(1-128,defualt 3): ` `default` &nbsp;&nbsp;&nbsp;&nbsp; Choose Defualt 

- `First Sector :` `default` &nbsp;&nbsp;&nbsp;&nbsp; Choose Default 

- `Last sector, +/-sectors or +/-size{K,M,G,T,P} : ` `defualt`&nbsp;&nbsp;&nbsp;&nbsp;  Rest of Disk for /home
<br>

- `Command (m for help): ` `w` &nbsp;&nbsp;&nbsp;&nbsp; Write Changes

<br>

`fdisk -l`  &nbsp;&nbsp;&nbsp;&nbsp; For Checking Partitions

---
<br>

#### Formatting 
`mkfs.fat -F32 /dev/sda1`  &nbsp;&nbsp;&nbsp;&nbsp; Formatting UEFI

`mkfs.ext4 /dev/sda2`  &nbsp;&nbsp;&nbsp;&nbsp; Formatting /root

`mkfs.ext4 /dev/sda3`  &nbsp;&nbsp;&nbsp;&nbsp; Formatting /home

---
<br>

#### Mounting
`mount /dev/sda2 /mnt`  &nbsp;&nbsp;&nbsp;&nbsp; Mounting /root

`mkdir -p /mnt/boot/EFI`  &nbsp;&nbsp;&nbsp;&nbsp; Making`boot`and`EFI` Directory in `/mnt`

`mount /dev/sda1 /mnt/boot/EFI` &nbsp;&nbsp;&nbsp;&nbsp; Mounting EFI

`mkdir /mnt/home` &nbsp;&nbsp;&nbsp;&nbsp; Making directory for home

`mount /dev/sda3 /mnt/home` &nbsp;&nbsp;&nbsp;&nbsp; Mounting home

---
<br>

### Installing
`pacstrap /mnt base linux linux-firmware vim` &nbsp;&nbsp;&nbsp;&nbsp; Installing packages in /mnt

---
<br>

#### Fstab

`genfstab -U /mnt >> /mnt/etc/fstab` &nbsp;&nbsp;&nbsp;&nbsp; Generating fstab file

`cat /mnt/etc/fstab` &nbsp;&nbsp;&nbsp;&nbsp; Just checking Partitions

---
<br>

#### Entering OS
`arch-chroot /mnt`  &nbsp;&nbsp;&nbsp;&nbsp; Change root to /mnt

---
<br>

#### Makeing Swap File
`fallocate -l 2GB /swapfile` &nbsp;&nbsp;&nbsp;&nbsp; Making Swap File (2GB is Enaugh)

`chmod 600 /swapfile` &nbsp;&nbsp;&nbsp;&nbsp; Changing swapfile perm

`mkswap /swapfile` &nbsp;&nbsp;&nbsp;&nbsp; making swap file

`swapon /swapfile` &nbsp;&nbsp;&nbsp;&nbsp; Turning swap on

<br><br>

`vim /etc/fstab` &nbsp;&nbsp;&nbsp;&nbsp;  Adding swap to fstab

###### add following line to end of /etc/fstab

`/swapfile none swap 0 0`

---
<br>

#### Time

`ln -sf /usr/share/zoneinfo/Asia/Tehran /etc/localtime` &nbsp;&nbsp;&nbsp;&nbsp; Changing time

`hwclock --systohc` &nbsp;&nbsp;&nbsp;&nbsp; Syncing hardware time

---
<br>

#### Locale

`vim /etc/locale.gen`

###### uncommnet `#en_US.UTF-8 UTF-8` from `/etc/locale.gen`

`locale-gen` &nbsp;&nbsp;&nbsp;&nbsp; Generating Locale File

<br><br><br>

`vim /etc/locale.conf` &nbsp;&nbsp;&nbsp;&nbsp;  Adding Lang to Locale

###### Add `LANG=en_US.UTF-8` to `/etc/locale.conf`

---
<br>

#### Host

`vim /etc/hostname` Name for PC

###### add `arch` to `/etc/hostname`

<br><br><br>

`vim /etc/hosts` &nbsp;&nbsp;&nbsp;&nbsp; Adding hosts

###### add `127.0.0.1            localhost` to `/etc/hosts`

###### add ```::1            localhost``` to `/etc/hosts`

###### add `127.0.1.1            arch.localdomain arch` to `/etc/hosts`

---
<br>

### Root

`passwd`  &nbsp;&nbsp;&nbsp;&nbsp;  Changing Root Password

---
<br>

#### Grub
`pacman -S grub efibootmgr networkmanager network-manager-applet wireless_tools wpa_supplicant dialog os-prober mtools dosfstools base-devel linux-headers` &nbsp;&nbsp;&nbsp;&nbsp;    Then Accept defualts

<br><br>

`grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=GRUB`  &nbsp;&nbsp;&nbsp;&nbsp;  Installing grub

##### If Getting error on above command try : `grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=GRUB --no-nvram --removale`

`grub-mkconfig -o /boot/grub/grub.cfg`  &nbsp;&nbsp;&nbsp;&nbsp;  creating grub config

---
<br>

#### Unmounting
`exit`  &nbsp;&nbsp;&nbsp;&nbsp;  exit chroot

`umount -a` &nbsp;&nbsp;&nbsp;&nbsp; unmount all

---
#### Reboot
`reboot`

---
<br><br>

## After Reboot :

#### Internet
```sh
systemctl start NetworkManager 
systemctl enable NetworkManager
```

---

#### User
`useradd -m -G soltan` &nbsp;&nbsp;&nbsp;&nbsp; adding `soltan` to users

`passwd soltan` &nbsp;&nbsp;&nbsp;&nbsp; changing password `soltan`

<br><br>

`EDITOR=vim visudo` &nbsp;&nbsp;&nbsp;&nbsp; Adding Perm to `soltan`

###### uncommnet `# %wheel ALL=(ALL) ALL`

---
<br>

#### GPU

- ###### Intel : `pcman -S xf86-video-intel`

- ###### AMD : `pcman -S xf86-video-amdgpu`

- ###### NVIDIA : `pcman -S nvidia nvidia-utils`

---
<br>

#### Xorg

`pacman -S xorg` &nbsp;&nbsp;&nbsp;&nbsp; Accept All Defaults

---
<br>

## Display Manager

- ###### Gnome : `pacman -S gdm`

- ###### KDE : `pacman -S sddm`

<br>

- ###### Gnome : `systemctl enable gdm`

- ###### KDE : `systemctl enable sddm`

---
<br>

## Desktop Enviorment

- ###### Gnome : `gnome gnome-extra`

- ###### KDE : `pacman -S plasma kde-applications xdg-user-dirs packagekit-qt5`

---
<br>

#### Reboot

`reboot`
<br><br><br><br>

#### Author
###### Amirhossein Abazari
