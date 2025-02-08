# What is this about?

I have been requested not long ago to repair a laptop from e-waste for a peer. The laptop? A MacBook Pro from 2017. The issue? A firmware lock.

This is the first time I have ever attempted to crack a firmware lock... and it was also one of the harder models to crack.

The MacBook Pro is from the lineup of models with the T1 chip. As you know, the T1 chip is a security chip made by Apple to handle Secure Boot, Touch ID, Apple Pay, etc. Part of the fuel of the fire which is Apple's closed ecosystem, which if you are familiar with Apple's methodology, is quite tight. Making it a difficult nut to crack.

Looked online, found a bunch of SPI EEPROM tutorials, etc etc.

Tried Apple's official tools and had zero luck. I tried just about every single recovery method in the book. Nope, didn't work. I tried DFU... Wait... T1 MacBook Pros don't even have DFU.

(Yeah, I've tried all of them. Didn't even show up in Apple Configurator 2)

Decided instead to do a BIOS dump and reverse engineer the BIOS myself.

# Opening up the bad boy

Alright, I pry this MacBook open. Look inside, and it looks like alien hardware. Completely unlike what I am used to working with (Mostly older PCs)

![The Insides of a MacBook Pro, isn't it lovely (a lovely nightmare)](https://raw.githubusercontent.com/norasyeezys/T1_Super_Hack/refs/heads/main/images/20250124_232410.jpg)

Okay. I admit. The original reason I did this was because the firmware lock was so invasive, I had my doubts I was actually turning the machine off.

Apparently MacBook Pro batterys are connected to the motherboard by this bendy metal thing hidden below the covers (The cover is that black thing right underneath the battery). Not the typical plug n play commonly found in PCs.

So I unscrewed that, and then finally pulled away the battery plug.

I held the power button to drain the capacitance. Then I tried those same methods as earlier with no luck (power on while holding random keys, unplug, drain capacitance, repeat).

Here's a quirk. When a MacBook Pro from this generation has a firmware lock and is running on charger only... You need to press the power button more than once to turn it on.

Asked my peer if they didn't mind the risk of bricking the laptop. They didn't. So I dug right in.

# Finding the EFI Module

Alright, so the game plan is to find the EFI chip, extract a BIOS dump, crack the firmware and reflash the BIOS. Sounds simple on paper, right? But there's a lot that needs to happen.

First, I needed to find out where the EFI chip actually was. What did I do? I went on schematics and boardviews for the exact Mac Model.

![I found the chip ID!](https://raw.githubusercontent.com/norasyeezys/T1_Super_Hack/refs/heads/main/images/Screenshot%20from%202025-01-29%2022-03-22.png)

A glimpse at the blueprints (Just did Ctrl+F for "EFI") and we found the Part ID of chip. The ID is U6100.

If you don't know how to interpret IDs, here's a quick tip. R are Resistors, C are Capacitors, L are inductors, D are diodes, Q are Transistors, and *U are specialized components* (the big black squares on the motherboard).

Next step. Where is Part U6100? That is where the boardview comes in.

It was a .brd file. Which posed another issue. But then I found [OpenBoardView (Thanks a lot!)](https://github.com/OpenBoardView/OpenBoardView).

Pulled up the chip's location on the board and...

![I found the U6100 chip!](https://raw.githubusercontent.com/norasyeezys/T1_Super_Hack/refs/heads/main/images/Screenshot%20from%202025-01-29%2022-15-15.png)

It's on the other side of the board. The one that isn't facing you when you first open up a MacBook Pro.

Which means, I had to actually take the logic board out of the computer. Thankfully it's not too difficult, just need to remove like 20 screws and unplug as many connectors as possible.

I will mention this. One of the connectors is glued onto the board. It's glued because it's attached to a hard part that sticks onto the board. Please try to take out that connector from the board when you unplug it. It is very small and fragile. It's called the "Mesa Connector" and it's really important since it has TouchID and especially because it connects to the button which is required to *TURN ON THE COMPUTER*. Learned it the hard way. So please don't try to break it. (Even though replacements are like 30$.)

Alright, I removed the board.

![Board Removal](https://raw.githubusercontent.com/norasyeezys/T1_Super_Hack/refs/heads/main/images/20250125_181623.jpg)

(In the picture you can see the bendy thing at the top that connects to the battery that I mentioned earlier)

Now I have to find the chip on the actual board. This is easy since I had the boardview, schematics, and already taken the board out.

It's right here.

![There it is...](https://github.com/norasyeezys/T1_Super_Hack/blob/main/images/20250130_221456.jpg)

You see it? It's the chip that says "Winbond 25Q64FVIQ". That's the EFI chip. It has 8 pins on the output as well.

It checks out with what I saw in the schematic.

Here's the pinout (it's basically the same for all models just the shape of the pins are different)

![You're welcome](https://raw.githubusercontent.com/norasyeezys/T1_Super_Hack/refs/heads/main/images/Screenshot%20from%202025-01-30%2022-28-38.png)

The fun part is about to commence...

# BIOS Dumping Time, or is it?

Recently got my SOIC8 chip clip in the mail. Also got a Raspberry Pi for me to do the actual BIOS flashing. (Will use a Pi that I already have, mind you.)

Alright, just need to map this pinout to that of my Raspberry Pi. The VCC pin has a supply voltage range of –0.6 to 4.6V. The operating range is between 2.7 and 3 V for some frequencies, and 3 and 3.6 V for others. The datasheet says this is a 3V EFI chip. I have no clue what frequency I will use.

Okay. MAYBE I could use a voltage divider to get exactly 3V for safety, but nah, screw it. Gonna use the 3.3 V output from the Raspberry Pi.

Alright the Ground is easy. But the rest is gonna be a while to figure out. (Just google raspberry pi pinout of whatever model you have)

In the meantime, will install the necessary tools like flashrom on the Pi.

But back to the chip clip! Make sure that the dot on the chip aligns with the colored wire, because the dot and colored wire both correspond to Pin 1, and they go counter clockwise the pins!

![Chip Clip: Attached](https://github.com/norasyeezys/T1_Super_Hack/blob/main/images/20250130_224725.jpg)

The chip clip is attached but I have my doubts I'm even doing this correctly. Look at the size difference between the chip clip's attachments and the chip. Might need mechanical adjustments to make this work. But let's see if this will do for now.

Time for the BIOS dump! (if my setup actually works)

# Pre-Testing

I'm sorry to surprise you. But before I use the MacBook, I tried a Thinkpad.

I needed a quick way to test the system, and apparently I was able to dump the BIOS of an older Thinkpad using Raspberry Pi and flashrom.

The chip topology of the Thinkpad's chip makes it easier. No need to solder unlike the Mac. However, I will try the Mac anyways just in case. It means,,, perhaps there is still hope after all.

I quickly got the gist of BIOS firmware dumps (You have to do it like 4 or 5 times because the checksums may be different. Also pinouts are basically the same between different EFI chips.)

# The Actual BIOS testing.
