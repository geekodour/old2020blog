---
title: "SSH to VM hangs and takes a long time to log in"
date: 2018-10-29T02:44:50+05:30
draft: false
categories:
  - sysadmin
slug: ssh-slows-gssapi
---

I am trying to learn ansible and I got quite frustrated at how long(15s) is it taking for my ubuntu vm to ssh into my centos vm.
So to know why is it taking so i used `ssh vagrant@ip_addr -vvv` to print things in debug mode.

The debug messages were these (trimmed ofc)

```
debug1: Unspecified GSS failure.  Minor code may provide more information                                                                                                                       
No Kerberos credentials available                                                                                                                          
                                                                                            
debug1: Unspecified GSS failure.  Minor code may provide more information                                                                                                                       
No Kerberos credentials available                           
debug1: Unspecified GSS failure.  Minor code may provide more information
debug1: Unspecified GSS failure.  Minor code may provide more information                                                                                                                       
No Kerberos credentials available

debug2: we did not send a packet, disable method
debug3: authmethod_lookup publickey
```

It was apparent that it's just lagging at the GSS failure thing, which probably relates to Kerberos which I have very less idea about.

I found a solution to this after some searching, 

Apparently adding the following line to `~/.ssh/config` on the machine you're ssh'ing from fixes the issue. It tells ssh not to look for GSS at all so it does not lag.

So add the following line to `~/.ssh/config` to fix this issue:

```
GSSAPIAuthentication no
```

