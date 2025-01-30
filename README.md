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

Now it's time for me to find the chip on the actual board. Then the fun really begins.
