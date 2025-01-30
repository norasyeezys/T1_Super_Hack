# What is this about?

I have been requested not long ago to repair a laptop from e-waste for a peer. The laptop? A MacBook Pro from 2017. The issue? A firmware lock.

This is the first time I have ever attempted to crack a firmware lock... and it was also one of the harder models to crack.

The MacBook Pro is from the lineup of models with the T1 chip. Making it a difficult nut to crack.

Looked online, found a bunch of SPI EEPROM tutorials, etc etc.

Tried Apple's official tools and had zero luck. T1 MacBook Pros don't have DFU, and just about every single recovery option failed.

(Yeah, I've tried all of them. Didn't even show up in Apple Configurator 2)

Decided instead to do a BIOS dump and reverse engineer the BIOS myself.

# Opening up the bad boy

Alright, I pry this MacBook open. Look inside, and it looks like alien hardware. Completely unlike what I am used to working with (Mostly older PCs)

![The Insides of a MacBook Pro, isn't it lovely (a lovely nightmare)](https://raw.githubusercontent.com/norasyeezys/T1_Super_Hack/refs/heads/main/images/20250124_232410.jpg)

Okay. I admit. The original reason I did this was because the firmware lock was so invasive, I had my doubts I was actually turning the machine off.

Apparently MacBook Pro batterys are connected to the motherboard by this bendy metal thing hidden below the covers.

So I unscrewed that, and then finally removed the battery.

I held the power button to drain the capacitance. Then I tried those same methods as earlier with no luck (power on while holding random keys, unplug, drain capacitance, repeat).

Here's a quirk. When a MacBook Pro from this generation has a firmware lock and is running on charger only... You need to press the power button more than once to turn it on.

Told my peer if they didn't mind the risk of bricking the laptop. They didn't. So I dug right in.

# Finding the EFI Module
