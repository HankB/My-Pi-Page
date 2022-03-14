# Powered Hub for SSD with 4B

If you perform a search for "pi 4B ssd problem", you will find a lot of hits that identify the problem (usually) as an incompatible SATA <-> USB 3 adapter. The suggested fix is to add "usb-storage.quirks=nnnn:nnnn" to the boot options in `/boot/cmdline.txt` to disable the UASP driver. I am loath to do this as it will cost performance. SSD performance is already hobbled by the USB translation and I'm all about getting the most performance out pf my Raspberry Pis. (*) I'm thrilled with the Pi 4B and want to get maximum performance from it and that means

* 64 bit OS
* SSD (using UASP driver)
* Mild overclock

I believe that the SSD issues are actually a power problem. Initially I did some testing with different SSDs and found that the ones that worked best were the cheap Inland SSDs from Micro Center (and available on Amazon.) Samsung 850 EVOs, Crucial MX 100s and MX 500s all would not work directly connected to Pi 4B USB3 port. They would work when connected through a powered USB hub. I theorize that the drives with faster write speeds were drawing more current when writing and that caused them to not operate correctly. I operated my "desktop Pi 4B" (running variously R-Pi OS, Debian Bullseye, Debian Bookworm, all 64 bit) without the need for a powered hub. I have a Pi 4B/4GB that runs headless, booting from a Seagate 600 240GB SSD and which runs fine without a powered hub. It's running P-Pi OS 10, 32 bit. I'm reluctant to upgrade to newer or 64 bit lest I have to add a powered hub to the mix.

(*) Actually, I have a number of Zeroes reading sensors and similar and these tasks load them so lightly that performance is not a concern.

## Qualification

I perform a number of tests to determine of the Pi 4/SSD (wioth or without powered hub) is stable.

* Does it boot? Necessary but not sufficient.
* confirm that the UAS driver is in use. `lsusb -t`
* Are there low voltage woanings in `dmesg` output following boot? (`dmesg|grep -i volt`)
* Run a series of disk benchmarks. Monitor dmesg output for voltage or SSD issues. I'm using either an approved Pi 4B power supply or the power supply that came with the hub. I almost never see low voltage warnings but when the SSD seems to be taking too long to respond, there may be messages in the logs indicating that the SSD is not responding and has bene reset.
    * `dd` benchmark (custom script.)
    * `time fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name="imara,240GB_Inland_Raspbian_64" --filename=test.dat --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75` (Remember to delete `test.dat` following the run.)
    * `time sudo bonnie++ -u root -d . -s 20000M -n 10:102400:1024:1024`
    * `time iozone -e -I -a -s 1000M -r 4k -i 0 -i 1 -i 2`

When a setup passes all of these tests without encountering any errors, I consider it useable.

## Powered Hub backfeed

In my searches for comments on using powered hubs, I found a lot of discussion about hubs that backfeed the Pi 4B through the USB 3.0 port. Apparently this can lead to two difficulties:

* Inability to reboot following shutdown (e.g. system hangs following `sudo shutdown -r now`.)
* The backfeed bypasses the normal input power conditioning (via the USB C connector) and can result in voltage spikes to the Pi if there is a power surge.

My testing will consist of 

* connecting a USB power meter between the Pi's USB connection and the hub to identify direction of power flow.
* Checking to see if the Pi can reboot with the powered hub connected. 

Test setup

* Pi 4B Rev 1.5 w/ 2GB RAM
* R-Pi OS 64 bit Desktop but w/out the extras and using the KDE desktop
* Pi powered from the hub.
* Inland Professional 240GB SSD
* Eluteng SSD <-> SATA adapter.
* Logitech wireless mouse with unifying receiver
* Ducky Shine keyboard (with LEDs off. \<rolls eyes\ker >)
* Meter "USB Digital Tester" model "J7-c"

With meter between hub and SSD the power flow indicator is right to left as viewed upside down. In the table below power flow in this direction will be indicated by positive current values. Negative values will indicate a backfeed.

|Hub|Volts|Current|Reboot?|notes|
|---|---|---|---|---|
|Wavlink WL-UH3042P1 REV.B|4.96 - 5.03|-0.17A|yes|Pi powered from "2.4A Quick Charge Port"|
|Amazon Basics||||Total dud. Looks like it powers up for a few seconds and then shuts down. Defective?|
|Amazon Basics|||yes|Works w/out meter connected. Cannot determine if it backfeeds.|
|Anker 7-Port USB 3.0 Data Hub AK-68ANHUB-BV7A-0004 ||||Can't boot|
|Sabrent|4.86|-0.20A|yes|Boot incomplete first try, Undervoltage reported.|
|Anker 4-Port Ultra Slim A7518 (Micro-USB power input)|5.05|-0.20A|yes|Power supplied by Canakit micro-USB wall wort|

## Conclusions

Unless The indicator on the meter is not telling me what I think it is telling me, all of the powered hubs I could test backfeed. Not all worked.

## Other options

* EYOOLD SATA <-> USB adapter with 2A power supply. Raspberry Pi can't boot from this adapter.
*  Gyges USB <-> SATA adapter with two USB cables. I thought it might provide enough power from 2 USB connectors on the Pi but it seems not to. It boots OK and runs for a while before the SSD hangs, likely due to insufficient power. This is the same behavior experienced with an unpowered (but otherwise working) adapter.f
