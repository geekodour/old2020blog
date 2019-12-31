---
title: "Sending mails with Gmail using Mutt and git send-email"
date: 2019-12-31T14:34:59+05:30
draft: false
categories:
  - Linux
  - Email
  - Tools
slug: mails-mutt-git-send-email-gmail
---

I am a huge fan of plain text email and for the last few days I've been testing the waters for contributing to the Linux kernel. Since kernel development happens in the mailing lists over plain text emails, I had an oppurtunity to setup some tools to help with plain text email exchange.

> I plan to update this blogpost every once in a while if I happen to upgrade my email setup, but for now this is what it is and this post describes how you can setup one just like mine, if you need. `BTW I'm using Arch`, so the package manager instructions in this post will be using `pacmac` but you should be able to replace them with whatever package manager you're using for your linux distribution.


### Creating a new email address
The first thing I did was to create a separate email address with Gmail. This is absolutely optional but I thought having a separate email would be a good idea because I get a lot of newletter emails on my primary email already.

So I created `myplaintextemailaddress@gmail.com` with gmail dot com.

### Install git and neomutt
I think you'll already have installed Git and Mutt if you're reading this but if not, here are the installation commands. I am using a neomutt instead of mutt for some extra benifits.

```
pacman -S git neomutt
```

### Enable IMAP on gmail
- Go to [Gmail Settings](https://mail.google.com/mail/u/0/#settings/general) of `myplaintextemailaddress@gmail.com` account.
- Inside the `Forwarding and POP/IMAP`>`IMAP access` section, enable IMAP.

### Generate some passwords.
This part is pretty simple but considering how your google account is setup, it may differ. The way I am doing it is:
- Setup 2FA on your google account.
- Setup two diffent [google app passords](https://support.google.com/accounts/answer/185833?hl=en) for `mutt` and `git-send-email`.
- Copy the passwords somewhere safe, we'll need them when configuring the clients.

> **Note:** You can only create(as of this writing) google app passwords if you have 2FA enabled.

Once that is done, we are ready to configure `mutt` and `git send-email`.

### Configuring `mutt`

Let's first configure mutt by adding these lines to `~/.muttrc`. If that file does not exist, you'll need to create it.

```
# file: ~/.muttrc

set imap_user="myplaintextemailaddress@gmail.com"
set imap_pass="generated_mutt_app_password_from_gmail"

set realname="YOUR_REAL_NAME"
set from="myplaintextemailaddress@gmail.com"
set smtp_url="smtps://myplaintextemailaddress@gmail.com@smtp.gmail.com:465/"
set smtp_pass="generated_mutt_app_password_from_gmail"
set smtp_authenticators="gssapi:login"

set folder=imaps://imap.gmail.com/
set spoolfile="+INBOX"
set record="+[Gmail]/Sent Mail"
set postponed="+[Gmail]/Drafts"
set postponed="+[Gmail]/Trash"

# You can use any gmail imap mailboxes
mailboxes =INBOX =[Gmail]/Sent\ Mail =[Gmail]/Drafts =[Gmail]/Spam =[Gmail]/Trash
```
After saving this file, you should have mutt and gmail working together.


### Configuring `git send-email`
Before we start configuring we need few more things, run the following two commands to avoid facing some fatal SSL warnings down the line.
```
sudo -H cpan Net::SMTP::SSL
sudo pacman -S perl-mime-tools perl-authen-sasl
```

Let's now configure for `git send-email`, for that you'll have to modify your `~/.gitconfig`.
Add the following lines to your gitconfig.
```
[user]
	email = myplaintextemailaddress@gmail.com
	name = YOUR_REAL_NAME

[sendemail]
    smtpserver = smtp.gmail.com
    smtpserverport = 587
    smtpencryption = tls
    smtpuser ="myplaintextemailaddress@gmail.com"
    smtppass="generated_git_send_email_app_password_from_gmail"
    suppresscc = self
    confirm="auto"
```
After this file is updated, try using `git send-email` and everything should be working as expected.

```
$ git send-email amazing-bug-fix.patch
```

Let me know if you find any errors in this post. Happy hacking! ./

