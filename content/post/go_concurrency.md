---
title: "Go concurrency"
date: 2019-04-19T07:08:55+05:30
draft: true
categories:
  - Golang
slug: go-concurrency
---

> Take what I am writing here with a grain of salt because I am extremly new to the language
> and this is everything I know about concurrency in Golang

If you have not already highly recommend

--

chan int (rw)
<-chan int (only read from, or close the chan)

Unbuffered channels vs Buffered

- Pipelining can be implemented with channels

difference between:
- c = make(chan int)
    - it returns a pointer which contains the address of the channel
- var c chan int [13:56]

Unbuffered channels are easy to reason about,
    - mutiple goroutines writing to the same channel no problem
        - but reading with multiple channels can be a problem
    - they provide both communication and synchronization
    - usually used for signalling (maybe wrong)
    - sometimes you don't even need to send anything to the channel if it's about signalling
        - just closing the channel is enough
        - close(c) can be replaced with c <- true
            - can it be replaced with c <- anything or false for that matter?
            - what does close actually do with a channel, what closeing mean
            - similarly what does make mean i.e returns a pointer to the channel
        - closed channels don't block, it directly panics on unbuffered channels
            - different with buffered channels
        - Note that it is only necessary to close a channel if a receiver is looking for a close.
        - After calling close, and after any previously sent values have been received, receive operations will return a zero value without blocking.
        - The close function records that no more values will be sent on a channel. 
    - nil channels are the opposite of closed channels
        - they always block
        - literally have the value nil for the (zero value) ??
        - useful in select
        - mychannel = nil
            - does not use <- here
    - in comes "select" statement
        - enables sending and recieveing on multiple channels at once [15:08]
            - i.e the select case statements can both be sending and recieving!
        - common idom for{select{}}
        - cummings : 7:41 without the select statement will this work?
        - [14:45] select statements are not switch statements
            - see  blocks
            - cases are evaluated
            - but can have other statements inside, eg. if statement along side case inside select statemenet
    - range for channels
        - read utill the channel becomes closed
            - is there implicit closing?
    
- buffered and unbuffered channels behave differently [9:43] 
    - buffered channel after close drains the data but what is the value of `ok`?

- 10:26 -> would not forloop run forever and ever?

