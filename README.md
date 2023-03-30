# agon-notes-and-examples

## Very Quick Start for Users of the Olimex Board

When you purchase a board from Olimex or one of their distributors, it will be shipped pre-flashed with two of the three components that together comprise the official Quark firmware: [agon-mos](https://github.com/breakintoprogram/agon-mos) and [agon-vdp](https://github.com/breakintoprogram/agon-vdp). To these, you must add a third: a copy of [agon-bbc-basic](https://github.com/breakintoprogram/agon-bbc-basic).

You are expected to download BBC Basic and examples from the FIRMWARE folder of the Olimex repository [AgonLight2/SOFTWARE/FIRMWARE/](https://github.com/OLIMEX/AgonLight2/tree/main/SOFTWARE/FIRMWARE) and to copy these to an SD card. The version of BBC Basic supplied here is 1.02. 

At the time of writing, the versions of the Quark components that are shipped preflashed are as follows:
* agon-mos: 1.02
* agon-vdp: 1.02

A version 1.03 in being tested of both of these at the moment but upgrading is only recommended if you have the necessary Zilog Smart Cable and the confidence to reflash the device if there are problems. Instructions for doing so can be found at [agon-mos](https://github.com/breakintoprogram/agon-mos)

Nonetheless, you can safely upgrade BBC Basic to version 1.03 direct from the repository at [agon-bbc-basic](https://github.com/breakintoprogram/agon-bbc-basic) without updating the other components. v1.04 however will not work with agon-mos and agon-vdp 1.02.

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

The official discussion group for the device is a Facebook group which is unfortunate for those who do not like to use that platform or who are willing to do so only reluctantly and use a false name and therefore risk having the account shut down for doing so because this is a breach of Community Guidelines. Facebook is the only forum in which the designers of the board and firmware participate.

Fortunately the [Official AGON QUARK Firmware Documentation Wiki](https://github.com/breakintoprogram/agon-docs) is regularly updated by Dean Belfield aka @breakintoprogram who is currently working on the official code itself and this source becomes more and more helpful by the day.

The designer of the hardware Bernardo Kastrup also updates the Agon Light page on his website [the ByteAttic](https://www.thebyteattic.com/p/agon.html) regularly.

While you miss out on the community by refusing to join Zuckerberg's empire and the ability to ask direct questions, important information shared there should appear shortly afterwards in one of those two external sources. 

The [agon-light topic](https://github.com/topics/agon-light) on github is also a useful way of finding examples and projects.    
