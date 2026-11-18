---
title: "Reconfigure Kobo - Part 1"
date: 2026-10-11
tags: ["OS", "HW", "FW"]
ai_note: "Dictated, AI-assisted, human-reviewed"
---

Today I want to share what I've been doing with my Kobo Libra 2. It started with an open-source reader I found online called KOReader. Once I got it running, I had another question. If the Kobo can already draw books and PDFs on its E Ink screen, can I make it draw whatever I want?

## Installing KOReader
The first thing I did was plug the Kobo into my computer over USB. It showed up as a drive, so I could browse the files on its user storage. What surprised me was that I could use that same drive to install something that wasn't a book.

I initially thought this was some kind of loophole. The interesting part is that it uses a mechanism Kobo already has for applying updates. The firmware looks for a package in a specific folder and applies it. In my setup, that mechanism installed NickelMenu, which gave me a way to launch KOReader. KOReader itself was a separate app that I copied onto the device.

After the Kobo restarted, I could still use the original Kobo interface as usual. The difference was that I now had a menu entry for KOReader.

## What I could do with it
The Kobo runs Linux, and KOReader made it feel a little more like a small Linux device to me. Instead of going through the usual book library, I could open a folder and see what was inside. I could also change settings from its menus or turn on an SSH server when I wanted to connect to the device from my computer. The KOReader source code itself is written by Lua.

Of course, I could also copy a PDF over and actually read it. But I quickly noticed the difference between a PDF and an EPUB. With a typical EPUB, I can make the text bigger and the lines wrap to fit the screen. When I view a PDF in its original layout, zooming makes the whole page bigger. The text is easier to see, but part of the page can end up off-screen. On a small display, those are two very different things.

## Next step
What I really wanted to see was whether I could put one of my own images on the screen. So I started looking through the KOReader source code. I made some changes, put my modified copy back on the Kobo, and tried getting it to display an image I chose.

That's the part I want to get into next. How do I go from an image file on my computer to something that actually shows up on the Kobo's E Ink screen?
