# Pi 4B Overclocking

References at <https://www.seeedstudio.com/blog/2020/02/12/how-to-safely-overclock-your-raspberry-pi-4-to-2-147ghz/> and <https://raspberryexpert.com/overclock-raspberry-pi-4/>

This is on Debian Buster, not R-Pi OS. Debian responds to the same overclocking techniques. (Debian Testing AKA Bookworm does not and seems to run very slow.) 

One important aspect of running the Pi 4B in general is cooling. My preference is the Flirc case. It provides passive cooling via the aluminum housing and has kept my Pi 4B within reasonable limits under all tested conditions.

Overclocks will be qualified with several benchmarks intended to stress CPU, GPU and storage. These include

* `stress` (`apt-get install stress`)
* `cpuburn-a53` (`wget https://raw.githubusercontent.com/ssvb/cpuburn-arm/master/cpuburn-a53.S`, <https://github.com/ssvb/cpuburn-arm>)
* `s-tui` (`apt-get install s-tui`)
* 

## Status work in progress
