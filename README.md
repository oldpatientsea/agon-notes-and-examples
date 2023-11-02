# agon-notes-and-examples

Note, this is now a little out of date in that version numbers have changed and the new Console8 has been released. I will update after the new Quark firmware release.

## Very Quick Start for Users of the Olimex Board

When you purchase a board direct from Olimex or one of their distributors such as the Pi Hut in the UK, it will be shipped pre-flashed with two of the three components that together comprise the official Quark firmware.

The first two elements are embedded in memory on the device. The third will sit on the micro SD card and it is up to you to install it. 

At the time of writing, the versions of the Quark components that are shipped with the device are as follows:
* [agon-mos](https://github.com/breakintoprogram/agon-mos): version 1.02 is flashed to the eZ80
* [agon-vdp](https://github.com/breakintoprogram/agon-vdp): version 1.02 is flashed to the ESP32 

To install the third element, you are instructed to download BBC Basic and examples from the FIRMWARE folder of the Olimex repository [AgonLight2/SOFTWARE/FIRMWARE/](https://github.com/OLIMEX/AgonLight2/tree/main/SOFTWARE/FIRMWARE) and to copy these to a SD card which you then insert into the device to complete the process. The version of BBC Basic supplied here is 1.02. 

By adding v1.02 of BBC Basic to the micro SD card the firmware is complete and the machine is ready to go.

(Strictly speaking the device is functional without BBC Basic but of limited use. It will complain if no SD card is inserted.)

You can immediately and safely upgrade BBC Basic to version 1.03 copying it direct from the repository at [agon-bbc-basic](https://github.com/breakintoprogram/agon-bbc-basic) without updating the other components. 

There is also a version 1.04 of BBC Basic however this will not work with agon-mos and agon-vdp 1.02.

Agon-mos and agon-vdp version 1.03 is being tested at the moment but upgrading to a release candidate (eg 1.03-RC2) is only recommended if you have the necessary Zilog Smart Cable and the confidence to reflash the device if there are problems. If you feel up to this, instructions for doing so can be found at [agon-mos](https://github.com/breakintoprogram/agon-mos)

There are good reasons to do so which will be explained below. And there are also good reasons to wait until the code is stable. There is nothing wrong in having a play with the device as it ships not least because it may make you appreciate the improvements coming in the next release. You can still have a lot of fun with v1.02 of the firmware and things are only going to get better.

## Caveat

A good many instructions and some example code you may find will have been updated for agon-mos and agon-vdp 1.03 already and will not work with version 1.02.

Sometimes you can work around this by checking out an older version of the code from their respective repositories, eg to use Lennart Benschop's [agon-forth](https://github.com/lennart-benschop/agon-forth) you need to checkout a version of the code written before the keyboard code updates for 1.03.

```
git clone https://github.com/lennart-benschop/agon-forth
cd agon-forth
git checkout 3f89f8dc2909cef9faa17b238193859e8cc2d6e8
```

In other cases, it may be worth waiting for agon-mos 1.03 / agon-vdp 1.03 / agon-bbc 1.04 to be ready (or if you are brave, taking part in the beta testing). There are a number of inconsistencies with BBC Basic as it was on the BBC Micro and for which numerous code examples already exist: for example, the original BBC Basic indexes for graphics from the bottom left corner (that is, the coordinates 0,0 refer to the bottom left) whereas the initial versions of the Agon BBC Basic index from the top left. Other changes are coming to the graphics modes which could potentially break any code you write now.

## Where to Find Information

The official discussion group for the device is a Facebook group which is unfortunate for those who do not like to use that platform or who are willing to do so only reluctantly and use a false name and therefore risk having the account shut down for doing so because this is a breach of Community Guidelines. Facebook is the only forum in which the designers of the original board and firmware participate.

There are two discords for programmers - the first of which is controlled by a reseller of Agon hardware who refuses to allow links to other vendors or discussion platforms. Most of the core contributors to the firmware can be found on a second, independent discord that does not have these limitations: [Agon and Console8 Community](https://discord.gg/unqWBbKvd6)  

The [Official AGON QUARK Firmware Documentation Wiki](https://github.com/breakintoprogram/agon-docs) is regularly updated by Dean Belfield aka @breakintoprogram who is currently working on the official code itself and this source becomes more and more helpful by the day.
The designer of the hardware Bernardo Kastrup also updates the Agon Light page on his website [the ByteAttic](https://www.thebyteattic.com/p/agon.html) regularly.

While you miss out on the community by refusing to join Zuckerberg's empire and the ability to ask direct questions, important information shared there should appear shortly afterwards in one of those two external sources. 

The [agon-light topic](https://github.com/topics/agon-light) on github is also a useful way of finding examples and projects.    
