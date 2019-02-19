权限控制之sudo



```
#cat /etc/sudoers

## Sudoers allows particular users to run various commands as
## the root user, without needing the root password.
##
## Examples are provided at the bottom of the file for collections
## of related commands, which can then be delegated out to particular
## users or groups.
## 
## This file must be edited with the 'visudo' command.

## Host Aliases
## Groups of machines. You may prefer to use hostnames (perhaps using 
## wildcards for entire domains) or IP addresses instead.
# Host_Alias     FILESERVERS = fs1, fs2
# Host_Alias     MAILSERVERS = smtp, smtp2

## User Aliases
## These aren't often necessary, as you can use regular groups
## (ie, from files, LDAP, NIS, etc) in this file - just use %groupname 
## rather than USERALIAS
# User_Alias ADMINS = jsmith, mikem


## Command Aliases
## These are groups of related commands...

## Networking
# Cmnd_Alias NETWORKING = /sbin/route, /sbin/ifconfig, /bin/ping, /sbin/dhclient, /usr/bin/net, /sbin/iptables, /usr/bin/rfcomm, /usr/bin/wvdial, /sbin/iwconfig, /sbin/mii-tool

## Installation and management of software
# Cmnd_Alias SOFTWARE = /bin/rpm, /usr/bin/up2date, /usr/bin/yum

## Services
# Cmnd_Alias SERVICES = /sbin/service, /sbin/chkconfig

## Updating the locate database
# Cmnd_Alias LOCATE = /usr/bin/updatedb

## Storage
# Cmnd_Alias STORAGE = /sbin/fdisk, /sbin/sfdisk, /sbin/parted, /sbin/partprobe, /bin/mount, /bin/umount

## Delegating permissions
# Cmnd_Alias DELEGATING = /usr/sbin/visudo, /bin/chown, /bin/chmod, /bin/chgrp 

## Processes
# Cmnd_Alias PROCESSES = /bin/nice, /bin/kill, /usr/bin/kill, /usr/bin/killall

## Drivers
# Cmnd_Alias DRIVERS = /sbin/modprobe

# Defaults specification

#
# Disable "ssh hostname sudo &lt;cmd&gt;", because it will show the password in clear. 
#         You have to run "ssh -t hostname sudo &lt;cmd&gt;".
#
Defaults    requiretty

#
# Refuse to run if unable to disable echo on the tty. This setting should also be
# changed in order to be able to use sudo without a tty. See requiretty above.
#
Defaults   !visiblepw

#
# Preserving HOME has security implications since many programs
# use it when searching for configuration files. Note that HOME
# is already set when the the env_reset option is enabled, so
# this option is only effective for configurations where either
# env_reset is disabled or HOME is present in the env_keep list.
#
Defaults    always_set_home

Defaults    env_reset
Defaults    env_keep =  "COLORS DISPLAY HOSTNAME HISTSIZE INPUTRC KDEDIR LS_COLORS"
Defaults    env_keep += "MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE"
Defaults    env_keep += "LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES"
Defaults    env_keep += "LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE"
Defaults    env_keep += "LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET XAUTHORITY"

#
# Adding HOME to env_keep may enable a user to run unrestricted
# commands via sudo.
#
# Defaults   env_keep += "HOME"

Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin

## Next comes the main part: which users can run what software on 
## which machines (the sudoers file can be shared between multiple
## systems).
## Syntax:
##
##      user    MACHINE=COMMANDS
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere 
root    ALL=(ALL)       ALL

## Allows members of the 'sys' group to run networking, software, 
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL

## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

## Allows members of the users group to mount and unmount the 
## cdrom as root
# %users  ALL=/sbin/mount /mnt/cdrom, /sbin/umount /mnt/cdrom

## Allows members of the users group to shutdown this system
# %users  localhost=/sbin/shutdown -h now

## Read drop-in files from /etc/sudoers.d (the # here does not mean a comment)
#includedir /etc/sudoers.d
```



```
# vim /etc/sudoers.d/rd_sudoer

# User Aliases
User_Alias RD_ADMINS = rd

# Command Aliases
Cmnd_Alias RD_CMD =/bin/grep, /usr/bin/tailf, /usr/bin/tail, /bin/cat, /usr/sbin/iotop, /usr/sbin/iftop, /usr/bin/du, /usr/sbin/lsof, /usr/sbin/ss, /usr/bin/netstat, /usr/sbin/tcpdump, \
/usr/bin/jstat, /usr/bin/jinfo, /usr/bin/jstack, /usr/bin/jmap

#Runas Alias
Runas_Alias    RD_OP_USER =root

#Authorization
RD_ADMINS  ALL=(RD_OP_USER) NOPASSWD: RD_CMD
```



说明：

```
系统文档推荐的做法，将修改写在/etc/sudoers.d/目录下的文件中。
如果使用这种方式修改sudoers，需要在/etc/sudoers文件的最后行，加上#includedir /etc/sudoers.d一行(默认已有):
#includedir /etc/sudoers.d
注意了，这里的指令#includedir是一个整体, 前面的#号不能丢，并非注释，也不能在#号后有空格。
任何在/etc/sudoers.d/目录下，不以~号结尾的文件和不包含.号的文件，都会被解析成/etc/sudoers的内容。

# This will cause sudo to read and parse any files in the /etc/sudoers.d
# directory that do not end in '~' or contain a '.' character.

# Note that there must be at least one file in the sudoers.d directory (this
# one will do), and all files in this directory should be mode 0440.

# Note also, that because sudoers contents can vary widely, no attempt is
# made to add this directive to existing sudoers files on upgrade.

# Finally, please note that using the visudo command is the recommended way
# to update sudoers content, since it protects against many failure modes.

```

