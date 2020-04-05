---
title: "Painting With Hex"
date: 2020-04-05T17:04:07+01:00
draft: false
tags: ["Breakout", "Tools"]
categories: ["Tools"]
---

# Background

As some of you may already know there is an old breakout technique where by creating a BMP in paint with particular colours can be used to dictate ascii commands into a file.

**Example:**

1. Open MSPaint.exe and create a canvas 5px by 1px and zoom all the way in.
1. Now using the colour picker. Set the Red, Green, Blue values of pixels from left to right to be:
    1. (10,0,0)
    1. (13,10,13)
    1. (100,109,99)
    1. (120,101,46)
    1. (0,0,101)
1. Save the image as a 24-bit Bitmap (*.bmp,*.dib)
1. Change the extension from .bmp to .bat and run.

![MSPaint](/img/Paint3.png)

As you can see in the image above. We've created the 5 required pixels, saved as a BMP and converted to a Bat file and when it's executed it's opened up and run cmd.exe so all good!


## Now what on earth is going on here?

When you convert the RGB colours above to Hex you get the following:

1. (10,0,0) -> 0x0A0000
1. (13,10,13) -> 0x0D0A0D
1. (100,109,99) -> 0x646D63
1. (120,101,46) -> 0x78652E
1. (0,0,101) -> 0x000065

Now the first pixels (6 bytes) are used to setup the BMP image after that we get into the meat of the command. First off is ` 0x646D63 ` if we convert this to ascii we get `dmc` which unless your a hiphop fan isn't quite right. So it's in little endian format.

So if we go an convert the rest of the pixels we should get the following:

1. (10,0,0) -> 0x0A0000
1. (13,10,13) -> 0x0D0A0D
1. (100,109,99) -> 0x646D63 -> dmc
1. (120,101,46) -> 0x78652E -> xe.
1. (0,0,101) -> 0x000065 -> e

Note the padding required on the last pixel as it has to be 3 bytes long.

# What else can we paint?

So the technique above is well documented, and i've used it a few times however, it is getting a bit long in the tooth. Nether the less on a breakout not too long ago we came across a reason to use it, unfortunately cmd.exe was disabled so why not see if we can run something else? Like Powershell?

So as we know from above the command needs to be broken down into 3 byte chunks, turned into little endian, padded and converted to hex then RGB.

So powershell.exe would be: 

1. (10,0,0) -> 0x0A0000 -> (10,0,0)
1. (13,10,13) -> 0x0D0A0D -> (13,10,13)
1. pow -> wop -> 0x776f70 -> (119,111,112)
1. ers -> sre -> 737265 -> (115,114,101)
1. hel -> leh -> 6c6568 -> (108,101,104)
1. l.e -> e.l -> 652e6c -> (101,46,108)
1. xe -> ex -> 006578 -> (0,101,120)

So lets create a new BMP that is 7px by 1px and repeat.

![Powershell.exe](/img/Powershell.png)

# Lets automate this
As much as I love doing these things manually I find it far better to automate them. So knocked together a quick python script that will allow you to specify any command and it will generate the required RGB codes for you. (Still got to create that image yourself though!)

![BMP_Execute](/img/BMP_Execute.png)

The tool can be found here [BMP_Execute](https://github.com/nop-sec/BMP-Execute), Happy painting!





