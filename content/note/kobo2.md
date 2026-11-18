---
title: "Reconfigure Kobo - Part 2"
date: 2026-10-12
tags: ["OS", "HW", "FW"]
ai_note: "Dictated, AI-assisted, human-reviewed"
---


In Part 1, I got my Kobo Libra 2 to display an image of my choice. That was fun, but I still had to tell it when to do it. What I really wanted was to wake up in the morning and see my calendar and the weather already on the screen.

I used Codex to help write the first version. It could update the screen, and it even worked twice in a row. Then the Kobo went to sleep and didn't come back.

## How does the server know when the Kobo is awake?
Once I had KOReader running, I did most of the setup there. I needed the Kobo to get back on Wi-Fi when it woke up and have its SSH server ready so my server could connect. If either one was missing, the Kobo could be awake and still look offline from the server's point of view.

My first thought was to have the Kobo fetch whatever I wanted to display. What I ended up with was a little different. **My server could reach the Kobo while it was awake, but it couldn't send a message to wake it up.** So the Kobo needed its own alarm, and the server needed to know roughly when to start looking for it. This can acutally be done by writing the walk up time in the RTC timer to the device via ssh.

Around the scheduled time, the server would try to connect over SSH. It would retry a few times and give up if the Kobo didn't show up. Once it connected, the server rendered the content as an image (this can be pre-calculated), sent it to the Kobo, and had the Kobo display it.

## Why it stopped after two updates
The first version moved on to putting the Kobo back to sleep as soon as it displayed the image. What I needed was one more step before that. If the Kobo refreshed the screen at 8 a.m., it also had to set an alarm for noon. I used its real-time clock (RTC) for that. At noon, the Kobo would wake up on its own, the server would check for it again, and that update would set the next alarm.

The part that took me a while to debug was what counted as “awake.” A full startup and a brief wake from sleep looked like the same thing when I drew the loop on paper. **In practice, the code that carried the loop forward was tied to the full-startup path. On a brief wake, the next alarm wasn't reliably set.** Once an alarm was missed, there was nothing for the server to connect to at the next scheduled time. From my side, it just looked like the whole thing worked twice and then stopped.

The fix was to handle the resume path and set the next RTC alarm before letting the Kobo go back to sleep. **I also found a separate wake timer that could bring the Kobo up on its own every few days. Since that could wake it outside the schedule I wanted, I disabled that timer and kept the one my scheduler used.**

With those changes, each wake could set up the next one. The Kobo didn't have to stay awake between updates, and I didn't have to go over and press the power button to get the loop going again.

## What I want to put on the screen
Now, in the morning, I could see todays calender when I wake up and know the seqther and weather to know if I need to put the coat on. I can render images that I wished and a good night picture before bed. 

Later I'd like to make the screen interactive, maybe with a button I can tap to see something else. I haven't built that part yet. For now, I'm happy that I can leave the Kobo alone and come back to a different picture.
