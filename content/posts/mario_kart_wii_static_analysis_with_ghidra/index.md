+++
title = "Mario Kart Wii Static Analysis with Ghidra"
date = "2025-05-09"
+++

I have been massively interested in the modding scene for Mario Kart Wii for a long time now. Seeing this much dedication for a game that's 17 years old is quite rare, but what's rarer is the lengths people go to make these mods work on the antiquated hardware that is the Nintendo Wii.

While all of the custom tracks, characters and vehicles that people are somehow still making in ancient tools like BrawlBox (Yes, I know people use [BrawlCrate](https://github.com/soopercool101/BrawlCrate) now, but it's still a fork of BrawlBox) are very impressive, what's always drawn my attention more is the boring stuff. Things like custom track frameworks such as the original [CT-CODE](https://github.com/Chadderz121/wii-ct-code) or [LE-CODE](https://wiki.tockdom.com/wiki/LE-CODE), the many fun additions to the base game that can be found in modern distributions, the open-source [WiiLink WFC servers](https://wfc.wiilink24.com/) and the extensive features that can be found in [MKW-SP](https://mkw-sp.com/).

I have always taken interest in the tools that people develop for games like Mario Kart Wii that allow them to be extensible and expandable - all the cool things I just mentioned maintain the principle of allowing people to play more of Mario Kart Wii, by giving them training tools, letting modders create new custom tracks for years to come and letting players play online with the exciting prospect of hosting our own WFC servers (this one is especially cool to me - thanks WiiLink! No shade to Wiimmfi).

Obviously, the problem that comes with this side of modding Mario Kart Wii is that it is super difficult. Many of the early developments made "back in the day" (2009) by people like MrBean35000VR and Chadderz helped with the reverse engineering of contemporary Wii titles. I like to think it shows that, for whatever reason, we're a very determined community with a dedicated craft. For the amount of strides and developments the Mario Kart Wii brainiacs have had over the years, for someone like me, a (as I'm writing this) game development undergraduate with little to no experience in reverse engineering, it has still seemed very daunting to me to take a stab at implementing my own custom code into the game. It seems we are dealing with a fairly fragmented community and it feels like I have to pick up whatever pieces I can between abandoned GitHub repositories and - (*shivers*) private Discord servers. The mere thought of having to join another Discord server solely for documentation makes me throw up in my mouth a little.

Perhaps I am speaking from a lack of experience, and, I totally am. The advancements people have made with this game are rooted in a thorough understanding of the Wii's OS ([IOS](https://wiibrew.org/wiki/IOS)), it's CPU ([Broadway](https://en.wikipedia.org/wiki/Broadway_(processor))) and PowerPC assembly. The knowledge developers get for this kind of stuff can't be found readily available online; it's derived from the original CodeWarriors documentation that would have come with an SDK - y'know, what actual game developers for the Wii and GameCube used all the way back in the 2000's. You can argue that there is plenty of online resources for PowerPC assembly and low level learning in general on the web, but no one has ever, **EVER** said it was still easy, especially when the code you are dealing with is heavily obfuscated.

Either way, I thought I would still talk about the baby steps I took in this blog post. Thankfully, none of it is what I would describe as very complicated and I'm moreso talking about it out of interest and giving my own perspective rather than presenting a fancy tutorial or anything like that. If you are new to low level programming, I hope you pick up some small things along the way! Many people have gotten into low level programming and reverse engineering via video games and modding. I think it's a very fun and rewarding approach to computer science.

# Introduction: How does a Wii Game even Work?

Let's start with a super top-down approach. I'm going to guess that if you're reading this, you've emulated a Wii or GameCube game before with [Dolphin](https://dolphin-emu.org/). You may have noticed that some of the ROMs that you emulate have a .iso extension. an ISO file is just a disk image, as, surprise surprise, Wii and GameCube games came on optical discs! So Dolphin is capable of emulating games via this format. There are other formats that Wii and GameCube games come in, such as .wbfs for Wii, .gcm for GameCube, and .rvz and .ciso for both. These have their use cases and are generally reserved for reducing the gigantic file sizes that a disc image can create.

While emulation may have us used to console games being these giant monolithic blobs, if you pop them open with tools such as [WIT](https://wit.wiimm.de/), or just extract them with Dolphin, you will see they are structured like any other game you would play on your PC via Steam for example. A bunch of folders reserved for different types of assets and an executable binary that contains the game's compiled code, or at the very least its main method. Mario Kart Wii's executable, and many other Wii games' executable, is a file called [main.dol](https://wiki.tockdom.com/wiki/Main.dol). When you start a game on your Wii, think of your Wii opening the .dol file similar to how you would open a .exe file on a Windows machine to start a game.

![The filesystem of Mario Kart Wii shown in Dolphin Emulator](https://github.com/evanbuggy/buggy-site/blob/main/static/dolphin_filesystem.png?raw=true)

The main.dol file is machine code, so for any hope of making complex edits to the logic of Mario Kart Wii, we need a disassembler. This will translate it into PowerPC assembly. If you know anything about reverse engineering, you'll know that [Ghidra](https://ghidra-sre.org/) is typically the hobbyist's choice due to being free and open-source. Developed and maintained by the NSA, it was publically released in 2019. Once you have an extracted filesystem for Mario Kart Wii, the main.dol file will be located in the "sys" directory. There is also another file of interest, [StaticR.rel](https://wiki.tockdom.com/wiki/Filesystem/rel/StaticR.rel), located in the "files/rel" directory (To clarify: Most likely, a folder called "DATA" is the root of the filesystem. Most Wii games come with things like system updates on the disc, or in Mario Kart Wii's case, the Mario Kart Channel. These aren't necessary for us so anything not in the "DATA" folder we can ignore.)

![The Mario Kart Wii binaries and their paths.](https://github.com/evanbuggy/buggy-site/blob/main/static/mkw_binaries.png?raw=true)

StaticR.rel actually contains most of Mario Kart Wii's code - .rel files are relocatable object files used by the Wii. They allow for code to be loaded dynamically during runtime. For Mario Kart Wii, the contents of StaticR.rel remains loaded for the entire duration of the game past the health and safety screen. main.dol primarily contains library code (math, graphics and important functions related to the Wii's operating system). Now that we have our binaries, let's bring them into Ghidra!

# Using Ghidra

This is the part where I would write about how to bring the binaries into Ghidra, but thankfully, terorie made a fantastic post on [mariokartwii.com](https://mariokartwii.com) which explains the process so I don't have to! You can find it [here](https://mariokartwii.com/archive/index.php?thread-1193.html). The guide goes through basic installation instructions for Ghidra and the additional things you'll need to allow Ghidra to correctly read the binaries.

The guide also contains this fantastic line:

> You can close it back down again because sadly the NSA didn't include logic to parse .dol/.rel files, or to understand the Wii's Broadway CPU. Maybe we need more mkwii cybercrime, just saying.

Once you have both binaries in a project in Ghidra, you can perform analysis on both of them. You'll end up with a window that looks something like this:

![A CodeBrowser window in Ghidra.](https://github.com/evanbuggy/buggy-site/blob/main/static/ghidra_main_dol.png?raw=true)

What you might notice about both of these binaries is that their address range begins at 0x80004000. .dol files can only reach an address range of 0x80004000 to 0x81200000 as anything outside of this range is reserved for the Wii's OS.

Now that we have our binaries disassembled, let's take a look at one of the functions! Mario Kart Wii (sadly) does not come with any debug symbols but you can look at a symbol map [here](https://docs.google.com/spreadsheets/d/1gA5WmnEbPAeA1Lq4XUJg9qDwawky9hpNUv2n1wWRwno/edit?gid=1610171642#gid=1610171642). Notice the different columns; these correspond to the regional versions of the game. You'll find that the PAL version has by far the most symbols. This might be a bit strange if you come from other modding scenes where the NTSC-U version is generally the one used and accepted by the community. I'm going to go ahead and assume your copy of the game is the PAL version.

Using the symbol map, you can copy any of the addresses of the functions listed and have a look at them in Ghidra by pressing the 'G' key and pasting them. The Ghidra decompiler will decompile the selected function's assembly to C code so you can have a gander at something slightly more readable.

A problem arises once you start to take a look at more game specific functions; let's say you want to have a look at the logic for how the item roulette works when you pick up an item box. According to our symbol map, this function (decideItem) is located at 0x807bb42c. It's probably not going to be in main.dol, as we previously said that was reserved for library code, so it must be in StaticR.rel. However, we'll find that it's not there. In fact, the address is completely outside the range of StaticR.rel. What gives? As mentioned previously, StaticR.rel is relocatable - it is intended to be dynamic. The address from our symbol map is taken from a dynamic analysis; the game's memory at runtime.

# The Big Twist: Dynamic Analysis!

Our ol' reliable binary main.dol has no problem piecing together StaticR.rel during runtime. So what we can do to look at our decideItem function is go back to Dolphin and use its awesome memory management tools to get a memory dump of Mario Kart Wii. Enable "Debugging UI" in Dolphin settings. Then, open Mario Kart Wii and get to the license screen. Go to "View -> Memory" and then click on "Export" and then click "Dump MRAM". This will create a memory dump wherever you have your dump path set in Dolphin (Most likely in your Appdata folder if you're on Windows like me).

![Dumping MEM1 in Dolphin.](https://github.com/evanbuggy/buggy-site/blob/main/static/dolphin_mram_dump.png?raw=true)

We can import this memory dump (called mem1.raw) into Ghidra the same way we imported our 2 binaries earlier. Run analysis, Go to decideItem's address and voilà:

![The decideItem function in Ghidra, barely readable.](https://github.com/evanbuggy/buggy-site/blob/main/static/ghidra_decideitem.png?raw=true)

# Conclusion

Unfortunately, perhaps due to my own stupidity, exporting the changes you make in Ghidra as a patched binary doesn't seem to be possible. So making complicated changes inside the editor isn't feasible. But that approach is not very necessary anyway. A tool like Ghidra is, obviously, extremely useful for documenting how this game works and furthering reverse engineering efforts. But for making cool custom code projects, much more practical means have arisen in the meantime.

I have to give a mention of [Kamek](https://github.com/Treeki/Kamek/tree/master), a Wii code injection engine originally made for New Super Mario Bros. Wii, but has been [adapted](https://github.com/MelgMKW/Mario-Kart-Wii-Kamek-Documentation) for Mario Kart Wii by MelgMKW. Melg's version of Kamek is the backbone of their custom track distribution creator, [Pulsar](https://github.com/MelgMKW/Pulsar), which powers distributions such as [Retro Rewind](https://wiki.tockdom.com/wiki/Retro_Rewind). There is also the riidefi's [decompilation](https://github.com/riidefi/mkw) of Mario Kart Wii. While it hasn't seen some progress in a while as of writing this, it is still an awesome resource.

I was able to get Kamek working for NSMBW, but I wasn't able to adapt it on my own for Mario Kart Wii. Perhaps I'll come back to it when I am fully able to wrap my head around it. If I do, you can bet there will be a blog post on it.

# Bonus

Since I don't want to leave this post without making any actual changes to the game, I thought I would leave off with something at least a little fun. If you couldn't already tell by now, the Mario Kart Wii wiki is a fantastic resource for everything about the game. Like, absolutely everything. [This](https://wiki.tockdom.com/wiki/Filesystem/rel/StaticR.rel) page has some neat references for the contents of StaticR.rel. Let's have a look!

The first thing the page covers is the Track Order Tables the game uses to determine what order the player plays tracks in. There are different addresses for each single player mode. 0x0037fcd0 is the address for the table used for the order of tracks in Grand Prix. If we were viewing this in Ghidra, the address would be 0x8037fcd0 due to the memory shift after our analysis. For the sake of being able to edit the file, I will just be working in a hex editor.

According to the table on the wiki, the ID for for Moo Moo Meadows (referred to internally as farm_course) is 1, and the ID for Luigi Circuit (referred to internally as beginner_course) is 8. What we can do is just swap these values, so when we begin the Mushroom Cup in Grand Prix, the 1st course is Moo Moo Meadows and vice versa.

![Editing the table in HxD.](https://github.com/evanbuggy/buggy-site/blob/main/static/hxd_staticr_rel.png?raw=true)

Once we save and boot the game with our new StaticR.rel, we can start the Mushroom Cup and see our changes take effect. Moo Moo Meadows as the 1st race - as it was always meant to be.

![Moo Moo Meadows](https://github.com/evanbuggy/buggy-site/blob/main/static/moo_moo_meadows.png?raw=true)