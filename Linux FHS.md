# Filesystem Hierarchy Standard

Source: [Debian Wiki](https://wiki.debian.org/FilesystemHierarchyStandard)

## Overview

The process of developing a standard FileSystem hierarchy began in August 1993 with an effort to restructure the file and directory structure of Linux. The FSSTND (Filesystem Standard), a filesystem hierarchy standard specific to the Linux operating system, was released on February 14, 1994. Subsequent revisions were released on October 9, 1994 and March 28, 1995.

In early 1996, the goal of developing a more comprehensive version of FSSTND to address not only Linux, but other Unix-like systems was adopted with the help of members of the BSD development community. As a result, a concerted effort was made to focus on issues that were general to Unix-like systems. In recognition of this widening of scope, the name of the standard was changed to Filesystem Hierarchy Standard or FHS for short.

The FHS is maintained by the Free Standards Group, a non-profit organization consisting of major software and hardware vendors, such as HP, IBM and Dell. Still, the vast majority of the Linux distributions, including those developed by members of the Free Standards Group, do not follow this proposed standard. In particular, paths specifically created by the FHS editors, such as /media/ and /srv/, do not see widespread usage. Some Unix and Linux systems break with the FHS in favor of a different approach, as in Gobo Linux.

## Directory structure

All files and directories appear under the ?root_directory "/", even if stored on different physical devices.

A description of the hierarchy specified in the FHS



### Main Directories for Developers

#### /bin/

Essential command executable (binaries) for all users (e.g., cat, ls, cp)
(especially files required to boot or rescue the system)

#### /etc/

Host-specific system-wide configuration files (from et cetera)

#### /home/

Users' home directories

#### /lib/

Libraries essential for the binaries in /bin/ and /sbin/
(library required to boot or rescue the system)

#### /opt/

Add-on application software packages
Pre-compiled, non ".deb" binary distribution (tar'ed..) goes here.

- /opt/bin/ : Same as for top-level hierarchy
- /opt/include/ : Same as for top-level hierarchy
- /opt/lib/ : Same as for top-level hierarchy
- /opt/sbin/ : Same as for top-level hierarchy
- /opt/share/ : Same as for top-level hierarchy

#### /dev/

devices files (e.g., :/dev/null)

#### /tmp/

Temporary files

#### /usr/

Secondary hierarchy for shareable, read-only data (formerly from UNIX source repository, now from UNIX system resources)

(files that are not-required to boot or rescue the system)

- /usr/bin/ : Same as for top-level hierarchy
- /usr/include/ : Standard include files
- /usr/lib/ : Same as for top-level hierarchy
- /usr/sbin/ : Same as for top-level hierarchy
- /usr/share/ : Architecture-independent (shared) data
- /usr/src/ : Source code (to build debian packages. see also /usr/local/src/)
- /usr/X11R6/ : X Window System, Version 11 Release 6
- /usr/local/ : Tertiary hierarchy for local data installed by the system administrator
- /usr/local/bin : locally compiled binaries, local shell script, etc.
- /usr/local/src : Source code (place where to extract and build non debian'ized stuffs)

#### /var/

Variable data, such as logs, databases, websites, and temporary spool (e-mail..) files



### Other Directories

#### /boot/

Boot loader, kernels and initrd files

#### /lost+found/
Some files and fragment that were "recovered" during the previous fsck (Not part of FHS)

#### /mnt/
Temporarily mounted filesystems

#### /media/

Mount points for removable media such as CD-ROMs (appeared in FHS-2.3)

#### /proc/
Virtual filesystem documenting kernel and process status, mostly text files (e.g., uptime, network)

#### /root/
Home directory for the root user

#### /sbin/

System administrative binaries (e.g., init, route, ifup) (system binaries)
(files required to boot or rescue the system)

#### /selinux/

SE-Linux runtime settings (Not part of FHS).

#### /srv/

Site-specific data which is served by the system (Not part of FHS).

#### /sys/

The filesystem for exporting kernel objects.
(many /proc/* files should have been here...)
