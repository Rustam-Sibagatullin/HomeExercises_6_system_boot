1. Попасть в систему без пароля несколькими способами

скриншоты выполнения работы находятся в файле "Change root password for Linux.docx"


2. Установить систему с LVM, после чего переименовать VG

история команд с вагрант машины:

    1  ls
    2  vgs
    3  vgrename VolGroupe00 OtusRoot
    4  vgrename VolGroup00 OtusRoot
    5  vgs
    6  cat /etc/fstab 
    7  sed -i 's/VolGroup00/OtusRoot/g' /etc/fstab 
    8  cat /etc/fstab 
    9  sed -i 's/VolGroup00/OtusRoot/g' /etc/default/grab
   10  sed -i 's/VolGroup00/OtusRoot/g' /etc/default/grub 
   11  sed -i 's/VolGroup00/OtusRoot/g' /boot/grub2/grub.cfg 
   12  vgs
   13  mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
   14  vgs
   15  exit
   16  vgs
   
 
аутпуты находятся в файле "renameVolGroup00"


3. Добавить модуль в initrd

скриншоты с картинкой находятся в файле "add module to initrd.docx"


1.	Install:
sudo apt install dracut-core
2.	Create derictory:
mkdir -p /usr/lib/dracut/modules.d/01test
3.	Add files module-setup.sh and test.sh.
Content of files:
*********************************
rustam4@rustam4-VirtualBox:/usr/lib/dracut/modules.d/01test$ cat module-setup.sh 
#!/bin/bash

check() {
    return 0
}

depends() {
    return 0
}

install() {
    inst_hook cleanup 00 "${moddir}/test.sh"
}
*********************************
rustam4@rustam4-VirtualBox:/usr/lib/dracut/modules.d/01test$ cat test.sh 
#!/bin/bash

exec 0<>/dev/console 1<>/dev/console 2<>/dev/console
cat <<'msgend'
Hello! You are in dracut module!
 ___________________
< I'm dracut module >
 -------------------
   \
    \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/
msgend
sleep 10
echo " continuing...."
*********************************
4.	Recreate initrd:
a.	Command mkinitrd not worked:
rustam4@rustam4-VirtualBox:/usr/lib/dracut/modules.d/01test$ mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
mkinitrd: команда не найдена
b.	Used 
dracut -f -v
c.	Check result:
$ sudo lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
[sudo] пароль для rustam4:       
test
5.	Change settings /etc/default/grub:
2 ways:
a.	Via vim: sudo vim /etc/default/grub.
b.	Or install grub customieser:
sudo add-apt-repository ppa:danielrichter2007/grub-customizer
sudo apt-get update
sudo apt-get install grub-customizer
c.	Result:
rustam4@rustam4-VirtualBox:/usr/lib/dracut/modules.d/01test$ cat /etc/default/grub
#If you change this file, run 'update-grub' afterwards to update
#/boot/grub/grub.cfg.
#For full documentation of the options in this file, see:
#info -f grub -n 'Simple configuration'

GRUB_DEFAULT="0"
GRUB_TIMEOUT_STYLE="hidden"
GRUB_TIMEOUT="10"
GRUB_DISTRIBUTOR="`lsb_release -i -s 2> /dev/null || echo Debian`"
#GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_CMDLINE_LINUX=""

#Uncomment to enable BadRAM filtering, modify to suit your needs
#This works with Linux (no patch required) and with any kernel that obtains
#the memory map information from GRUB (GNU Mach, kernel of FreeBSD ...)
#GRUB_BADRAM="0x01234567,0xfefefefe,0x89abcdef,0xefefefef"

#Uncomment to disable graphical terminal (grub-pc only)
GRUB_TERMINAL="console"

#The resolution used on graphical terminal
#note that you can use only modes which your graphic card supports via VBE
#you can see them in real GRUB with the command `vbeinfo'
#GRUB_GFXMODE="640x480"

#Uncomment if you don't want GRUB to pass "root=UUID=xxx" parameter to Linux
#GRUB_DISABLE_LINUX_UUID="true"

#Uncomment to disable generation of recovery mode menu entries
#GRUB_DISABLE_RECOVERY="true"

#Uncomment to get a beep at grub start
#GRUB_INIT_TUNE="480 440 1"

#GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"

GRUB_SAVEDEFAULT="false"
6.	Reboot
7.	Check output during restart:




