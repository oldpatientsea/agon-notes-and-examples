# Notes Gleamed from Facebook (Before My Account was Deleted)

I don't have the source of all of these and don't have the energy right now to create another fake profile so apologies if I quote you without crediting you properly.

### Agon Light Quick Points

    Quark Firmware is a suite of seperate firmwares files, some run on the eZ80 others on the ESP32.
    MOS is the firmware and operating system which is flashed to the eZ80.
    A Zilog cable and software, is needed for an Agon Light that doesn't have firmware installed (e.g. DIY Agon Kits, or if you manufacture you own).
    Once MOS v1.0 is installed there are utilities that can flash the Agon Light firmware, the Zilog Cable/Software is NOT needed.
    VDP - Is the firmware for the ESP32 video.ino is flashed to the ESP32 using arduino software. It utilises the fabGL library that was written for the ESP32.
    VDU commands control the ESP32 functions from within MOS.
    BBC Basic is launched from MOS, BBC Basic has a "*BYE" command that will return the Agon Light to MOS.
    CP/M uses a different VDP, video.ino (at this stage) file but still is launched from MOS, the new VDP 1.03 will be able to run BBC Basic and CP/M.
    An autoexec.txt file placed in the root of the SD Card can launch applications automatically.
    e.g.
    ----
    For example, to set keyboard to US, load BBC BASIC from the root folder, change to the test folder, then run BASIC
    SET KEYBOARD 1
    LOAD bbcbasic
    CD test
    RUN
    ---
SET KEYBOARD 0 for UK.

via Fred (?) on Agon Programmers Group.

### Graphics

Note it is not bitmapped graphics. Instead graphics is all run on ESP video, instructions (from MOS or language running on MOS, eg BASIC) are passed over serial/UART from CPU then executed.

This means you cannot create your own clever routines in Z80 code to update screen in a "raw" mode.

However it also means that the graphics are blisteringly fast and speed is the same when called from Assembler as when called from BASIC (is this true?)

	The ZX Spectrum and other 1980s computers have a dumb framebuffer, i.e. they share a video RAM on the same bus with the CPU. Some hardware circuit generates the video output from the contents of the video RAM. The CPU just has to write bytes into the video RAM to change what appears on the screen.
The Agon Light has a totally different architechture. The video RAM is not shared on the same bus. To change what is displayed on the screen the CPU has to send some instructions to the VDP via the serial link.
	To have a byte code compatible abstraction layer [and port ZX Spectrum gfx to Agon], one would need to create a pseudo-video framebuffer, then scan the said framebuffer 50x per second via the vblank interrupt and communicate the changes to the VDP. I did a back of the envelope calculation, and it's nowhere near feasible.



What is the VDP

	The VDP is a serial graphics terminal that takes a BBC Basic text output stream as input. The output is via the VGA connector on the Agon.

	It will process any valid BBC Basic VDU commands (starting with a character between 0 and 31).

	For example:

	VDU 25, mode, x; y; is the same as PLOT mode, x, y

[Source: https://github.com/breakintoprogram/agon-vdp]

Chapter 29 of BBC User Guide p.170-171 instructions on using VDU to create UDG (user defined graphics)

See also chapter 34 of BBC User Guide for more on VDU

See also
https://www.bbcbasic.co.uk/bbcbasic/manual/vdu.html#vdu25
https://www.bbcbasic.co.uk/bbcbasic/manual/vdu.html

Sixteen Colour Graphics Modes - appears to be true of default mode (1?) on Agon


    Foreground	Background	Colour
    COLOUR 0	COLOUR 128	Black
    COLOUR 1	COLOUR 129	Red
    COLOUR 2	COLOUR 130	Green
    COLOUR 3	COLOUR 131	Yellow
    COLOUR 4	COLOUR 132	Blue
    COLOUR 5	COLOUR 133	Magenta
    COLOUR 6	COLOUR 134	Cyan
    COLOUR 7	COLOUR 135	White
    COLOUR 8	COLOUR 136	Intensified Black
    COLOUR 9	COLOUR 137	Intensified Red
    COLOUR 10	COLOUR 138	Intensified Green
    COLOUR 11	COLOUR 139	Intensified Yellow
    COLOUR 12	COLOUR 140	Intensified Blue
    COLOUR 13	COLOUR 141	Intensified Magenta
    COLOUR 14	COLOUR 142	Intensified Cyan
    COLOUR 15	COLOUR 143	Intensified White

You can change the physical to logical colour mapping (palette) using the VDU 19 command. 

[ src: https://www.bbcbasic.co.uk/bbcbasic/manual/bbckey1.html#colour]



Up to 640 x 480 pixels, at up to 64 simultaneous colors. All graphic processing is handled by the ESP32 fairly independently from the eZ80. The latter only needs to send display list commands (coarse grained, high-level commands, with no bit-banging to video memory) to the ESP32, via a 384 kbps link. 

The graphics and sound capabilities are software defined (by the code running on the ESP32) and are constrained only by the physical processing speed limits of the ESP32 and, in practice, what the FabGL library is capable of. The only exception are the 64 colors: those are hardware limited by the RGB DAC.

The sound specs are the same as those of the FabGL library. Currently, BASIC makes use of only simple tones, but Agon is capable of a lot more. It's supported multiple channels since 1.02 (3)

Agon has 640 x 480 x 64 colors. Fixed palette.

The current video modes only have 16 distinct colours, but the hardware should be capable of 64 distinct colours, but this requires adapting the VDP firmware. The hardware has a 2-bit video DAC (four levels) for each of the R, G and B components.

current VDP does just the default fabgl vga-16 palette out of 64, so sprites are limited to that currently as well

The current version of vpd uses 16 colors only for all modes. Lennart Benschop and Dean Belfield are testing a VPD change allowing 64 colors for mode 2 (320x200) (not ready for prime time?)

coordinate system currently different to BBC - plan to make it configurable.

graphics coordinates are in screen pixels not OS Units and scale with the screen mode (whereas in Acorn graphics OS Units are independent of screen resolution, but are scaled to the current screen mode by particular eigen factors (which may be different in both X and Y directions depending on whether the pixels are square, tall or wide). So, on a BBC Micro, screen mode 0 (which is only monochrome, but is still 80-column and graphical, like on the Agon) has a resolution of 640×256 pixels, but its actual resolution is 1280×1024 OS Units. And if you change your program to use mode 1 instead (BBC mode 1 is 40-column: 320×256 pixels), then the pixels become twice as coarse horizontally, but the underlying plotting grid (the OS Units) is identical.

This will probably be made configurable in a later version.

SOUND is only of limited functionality (single tone, no envelope). The current VDU only supports a subset of the commands and modes once supported by BBC Basic, but adds some more like screen scrolling and sprites. You can look at the example programs in the tests directory to see what it can do.
Lennart Benschop It supports multiple channels. Envelope to follow.

 commands once removed from the CP/M version and added back to the Agon version, like MOVE, DRAW and SOUND.
 
 MOVE. DRAW, PLOT work largely the same as in classic versions of BBC Basic, and more can be found in the file MANUAL.md in
 
 What do these do? M is Mode as entered by user.
 
 	  111 IF M%=0 THEN VDU 29,320;240;:OD=128
  112 IF M%=1 THEN VDU 29,256;192;:OD=150
  113 IF M%=2 THEN VDU 29,160;100;:OD=256

### Editing


You can edit lines in BBC with *EDIT linenumber

A neat feature not a lot of folk know is that you can search for things so LIST IF MODE will list only lines that contain MODE. And combine with X-Y so LIST 100-500 IF HELLO should work

### Memory map

Programs in the MOS directory are supposed to be loaded into the &B0000..&B7FFF area (32kB), while normal 16-bit programs are allowed to use a 64kB area (mostly starting at &40000). Furthermore, non-MOS programs can load and run other MOS commands, which they could not do if they were also running in the &B0000 segment. Case in point: my editor 'nano' is a MOS command and it can be loaded by my Forth, which is NOT a MOS command.

BBC Basic needs a full 64K segment, so cannot be run from the MOS folder as a star command.


Agon Light memory map -

	https://docs.google.com/spreadsheets/d/1I4LCq9sk3zX_uLTfh4LDtnH-dFLfXeUynAzDGIgTutI/edit?fbclid=IwAR3ibE54MUoOPlJ2MmFoFb5Oj4GRRDcrG06gLCT-wDW0l5HHXudKMridAIQ#gid=0
	
	
# Deleted Manual.MD - commit 067cc5a
https://github.com/breakintoprogram/agon-vdp/commit/067cc5a563c53c42125b8b7f9e2b1f35f94bc105 

# manual

The VDP Graphics Terminal is based upon the BBC Micro VDU command.

Whilst it is tailored to work well with the Agon version of BBC Basic for Z80, the command set should be sufficiently rich for any application to render graphics on-screen.

## Interface

The interface between the eZ80 and the VDP is via a serial UART running at 384,000 baud.

## VDU Character Sequences

The VDU command is a work-in-progress with a handful of mappings implemented.

- `VDU 8`: Cursor left
- `VDU 9`: Cursor right
- `VDU 10`: Cursor down
- `VDU 11`: Cursor up
- `VDU 12`: CLS
- `VDU 13`: Carriage return
- `VDU 16`: CLG
- `VDU 17 colour`: COLOUR colour
- `VDU 18, mode, colour`: GCOL mode, colour
- `VDU 19, l, p, r, g, b`: COLOUR l, r, g, b
- `VDU 22, n`: Mode n
- `VDU 23, n`: UDG / System Commands
- `VDU 25, mode, x; y;`: PLOT mode, x, y
- `VDU 29, x; y;`: Graphics origin
- `VDU 30`: Home cursor
- `VDU 31, x, y`: TAB(x, y)
- `VDU 127`: Backspace

All other characters are sent to the screen as ASCII, unaltered.

## VDU 23, 0: VPD commands

VDU 23, 0 is reserved for commands sent to the VDP

- `VDU 23, 0, 1, n`: Set the keyboard locale (0=UK, 1=US, etc) 
- `VDU 23, 0, 2`: Request cursor position
- `VDU 23, 0, 3, x; y;`: Get ASCII code of character at character position x, y
- `VDU 23, 0, 4, x; y;`: Get colour of pixel at pixel position x, y
- `VDU 23, 0, 5, channel, waveform, volume, freq; duration;`: Send a note to the VDP audio driver
- `VDU 23, 0, 6`: Fetch the screen dimensions 

Commands 2 onwards will return their data back to the eZ80 via the serial protocol

## VDU 23, 1: Cursor display

- `VDU 23, 1, 0`: Disable the text cursor
- `VDU 23, 1, 1`: Enable the text cursor

## VDU 23, 7: Scrolling

- `VDU 23, 7, extent, direction, speed`: Scroll the screen

## VDU 23, 27: Sprites and Bitmaps

VDU 23, 27 is reserved for the bitmap and sprite functionality

### Bitmaps

- `VDU 23, 27, 0, n`: Select bitmap n
- `VDU 23. 27, 1, n, w; h; b1, b2 ... bn`: Load colour bitmap data into current bitmap
- `VDU 23, 27, 2, n, w; h; col1; col2; b1, b2 ... bn`: Load monochrome bitmap data into current bitmap
- `VDU 23, 27, 3, x; y;`: Draw current bitmap on screen at pixel position x, y

### Sprites

- `VDU 23, 27, 4, n`: Select sprite n
- `VDU 23, 27, 5`: Clear frames in current sprite
- `VDU 23, 27, 6, n`: Add bitmap n as a frame to current sprite
- `VDU 23, 27, 7, n`: Activate n sprites
- `VDU 23, 27, 8`: Select next frame of current sprite
- `VDU 23, 27, 9`: Select previous frame of current sprite
- `VDU 23, 27, 10, n`: Select the nth frame of current sprite
- `VDU 23, 27, 11`: Show current sprite
- `VDU 23, 27, 12`: Hide current sprite
- `VDU 23, 27, 13, x; y;`: Move current sprite to pixel position x, y
- `VDU 23, 27, 14, x; y;`: Move current sprite by x, y pixels
- `VDU 23, 27, 15`: Update the sprites in the GPU

## Serial Protocol

Data sent from the VPD to the eZ80's UART0 is sent as a packet in the following format:

- cmd: The packet command, with bit 7 set
- len: Number of data bytes
- data: The data byte(s)

Words are 16 bit, and sent in little-endian format

Packets:

- `0x00`: General Poll
- `0x01, keycode, modifiers`: Keyboard
- `0x02, x, y`: Cursor position
- `0x03, char`: Character read from screen
- `0x04, r, g, b, index`: Pixel colour read from screen
- `0x05, channel, success`: Audio play note acknowledgement
- `0x06, width, height, cols, rows, colours`: Screen dimensions - width and height are words

## Keyboard

When a key is pressed, a packet is sent with the following data:
- cmd: 0x01
- keycode: The ASCII value of the key pressed
- modifiers: A byte with the following bits set (1 = pressed):
```
0. CTRL
1. SHIFT
2. ALT LEFT
3. ALT RIGHT
4. CAPS LOCK
5. NUM LOCK
6. SCROLL LOCK
7. GUI
```


===============

Another day, another stupid question: when booting to MOS or BBC BASIC, can I assume MBASE is already set to $20? If I change that, will I still be subject to NMIs that could change it back, or do I have control over what gets executed? Which begs my last stupid question: are there NMIs getting generated in the default configuration, or is the NMI pin routed to one of the expansion or GPIO pins?


Dean Belfield
MB is &00 in MOS, &04 in BBC BASIC, and &0B if running a star command that runs in Z80 mode. Interrupts always run in ADL mode, and the only (new) code that calls a 16-bit interrupt handler is in BBC BASIC, and that preserves MB on entry, and restores when leaving. There are only two interrupts running, for UART and VBlank. The NMIs are not routed to GPIO AFAIAA.

==============

After building the MOS source code with ZDS II (using the debug configuration), a mos.hex file gets generated.

To flash the new MOS firmware to the AgonLight, is the next step to convert the .hex file to a .bin file using a tool like Hex2Bin and then to compute the CRC32 of the .bin file?

Jeroen Venema
Yes, that is the proper order building it yourself, not using the zilog programmer cable. The flash utility flashes a binary file and requires a CRC32 to proceed. At release, a .bin file will be provided with the corresponding CRC32

===============

PEEK and POKE


BBC BASIC didn't use the PEEK or POKE keywords, but had the ? operator and statement which had the same effect. So the statement ?128 = 0 is equivalent to POKE 128, 0, and the expression ?128 is equivalent to PEEK 128. However, it also had ! and $ which did 32-bit and string peeks and pokes and e.g. $128 = "HELLO" would write the ASCII bytes of "HELLO" into locations 128–132, and terminate the string with a carriage-return (0x0D) terminator in location 133.

Syntax such as A?3 could also be used and was equivalent to ?(A+3). This is obviously useful for packing and unpacking structures.


===============

Can I ask why graphics on the Agon are indexed from the top left corner (standard in just about every other programming language) and BBC basic indexes from the bottom left corner?

The latest soon to be released version of the Agon firmware already supports a BBC-like coordinate system where the bottom left is 0,0 and the top right is 1279, 1023. All coordinates are scaled to fit the current screen resolution. That will be the default and you can switch back to the old Agon behaviour if you want.

================
Please could someone list the graphics modes that the Agon Light supports?

SVGA_1024x768_60Hz
VGA_512x384_60Hz
VGA_320x200_75Hz
VGA_640x480_60Hz
[although for my monitor I have to change SVGA_1024x768_60Hz to SVGA_1024x768_75Hz]


I guess the most useful modes would be 320x200x64 colours and 640x480x16 colours. As they are both VGA resolutions, they should work with all monitors. I can see value in having some lower colour modes at these resolutions (e.g., 320x200x16 colours) if it would speed up graphics (specifically, blitting).
The only downside is that none of these resolutions divide easily to the 1280x1024 logical screen coordinates, but perhaps that’s not a problem in reality?

Julian Regel I've added 320x256x64 and 640x512x8 to my vdp. They are pretty stable and are a 4x and 2x integer scale to 1280x1024, respective

512x384 is 1:4 XGA (1024x768)

and 4:1 the (256x192) Speccy and C64, I think..

=================

Region scrolling support.
Currently you can scroll the whole screen in any mode both vertically or horizontally.
This is good and with the upgrade to 1152k baud rate for the serial link between ez80 and esp32 will allow us to scroll 4 rows of 40 cols text within a vertical retrace, making chracter tile based scrolling games possible on the Agon.
But, what if I need to insert a row in the middle of the screen?
Today that requires redrawing the screen from that line down. If you've used Lennart Benschop Nano editor, you have definitely noticed the flicker every time you add a new line in the middle of the text.
But, FabGL supports defining a scrolling region, it's just not available through VDP yet. With that support, you can define in the editor that the scrolling region is just the part of the text bellow the line you are about to create, issue the command to scroll it and then send 80 chars to VDP, all within the vertical retrace.
Adding it is pretty simple. I've changed my vdp to add a new command: VDU 23, 7, 2.
VDU 23, 7 is for scrolling. Currently, any value following the 7 is ignored but the comment mentions 0 and 1 as possible values. So I  used 2.
If you set it to 2, then VDP expects 4 16 bits values, in order: x1, y1, x2, y2 and then sets the scrolling region to that rectangle.
With that, any scroll command issued will scroll only that defined region.
Besides the benefits for the editor, in games you can now define the rectangle such that you have an upper static bar and even a lower one. Or, you could define different scrolling regions. Use your imagination 🙂
In case you want to try it out yourself, just replace the vdu_sys_scroll function in agon-vdp/video.ino with the one is this gist: https://gist.github.com/.../7764ca788fbe363a8539246d43af47a0
Try it out and let me know 🙂

I will integrate in the main code. Will tweak to make it BBC BASIC compliant; first set the scrolling window, then use VDU 23, 7, 0 with no parameters to scroll that window. VDU 23, 7, 1 will scroll the entire screen regardless. Will also be able to clear windows too.

thanks! From reading about vdu commands on https://www.bbcbasic.co.uk/bbcwin/manual/bbcwin8.html#vdu24, that would then be vdu 28 to set the text viewport and vdu 26 to reset then?

a I think so.

Also VDU 24 to set graphics viewport.

=====================

Is there a replacement for the GET(x,y)? Get character at x,y? I'm still on 1.02.
On the subject of firmware, is there an ETA for release?

Not yet (it'll be a BBC BASIC bin change) and no ETA. Nearing the end though. I've added a card for GET.

======================================

Bernardo says the display list approach to managing the display and audio is comparable to the Atari 8-bit home computers architecture - and a number of other home computers used that approach too, including MSX computers.


======================================

Once 1.03 is final and instructions are let loose on the masses, please tell them to flash the MOS first and then flash the VDP. Once MOS is upgraded by itself, you can no longer type commands, but you don't need to in order to flash VDP from the Arduino IDE. Of course somebody must first test whether this procedure actually works... upgrading from 1.02 to 1.03 (and Basic 1.04).

Specifically on Olimex - which USB port would I use to reflash VDP please? Is it the USB-C?
Nevermind - found it in Olimex Manual https://github.com/.../DOCUMEN.../AgonLight2-user-manual.pdf page 21, looks like you have to flip both jumpers and then use USB-C


Just confirming the notes, I will need to send the following character sequence to enable the VT100 terminal mode:
*VDU 23 0 255 to switch to terminal mode

yes. It's experimental and untested but does switch.


https://github.com/breakintoprogram/agon-vdp/issues/5?fbclid=IwAR2n9hG90oLO5nn-mN-cT0oU2AiGa2c531ommAaB8Ipx_7G73mHoRFWMbMU

=========================================

