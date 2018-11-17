---
title: "Trellis on arch linux"
date: 2018-11-15T21:39:13+05:30
draft: true
categories:
  - Wordpress
  - sysadmin
slug: tresslis-on-arch-linux
---

Trellis is like a boilerplate for Wordpress deployment with Ansible.
There are few gotchas when using the vagrant dev environment in Arch Linux

- NFS does not work
[Known issue](https://wiki.archlinux.org/index.php/Vagrant#.27vagrant_up.27_hangs_on_NFS_mounting_.28Mounting_NFS_shared_folders....29)
```
[nfsd]
vers3=y
udp=y
```
make sure the above exists in `/etc/nfs.conf` and then `sudo systemctl restart nfs-server.service`

- Ansible requires python 2
for some wierd ass reason all my ansible config works and i run python3 and this throws some error because of host machine using python3

**Solution:** use a python virtualenv for python2, make sure to close the shell and install ansible in this environment

