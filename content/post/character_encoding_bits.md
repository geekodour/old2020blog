---
title: "Character encoding bits"
date: 2018-07-04T10:04:42+05:30
categories:
  - ASCII
slug: character_encoding_bits
---

So I was just hanging around a discord group when someone asked this:

> What's the minimum number of bits I can use to encode an arbitrary ASCII string 8 characters long that only contains capital letters and numbers?
> I'm coming up with 42, but that seems like too many. Maybe my intuition for this is just wrong

So how did he come up with 42?

He's basically asking he as a set of 36 possibilities for a character (26+10)
and the length of the string he wants to fit those characters in is 8 characters long.

Therefore we have **36^8** number of possibile strings.

Now to find out the theoritical amount of bits it would take,
we do `log2(36^8) == 41.4 ~= 42`


Okay, let's try this with the ususal character set of ASCII(with 256 characters)

256 possibilities for each character,
let's take string length to be 1 character.

So number of possibile strings is **256^1** . Now let's log2 it.

> `log2(256^1) == log2(256) == 8`

So it will take 8 bits for one character of ASCII which is one byte. amirite.
