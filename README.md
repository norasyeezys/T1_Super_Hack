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
