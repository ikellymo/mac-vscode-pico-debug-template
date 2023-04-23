<i>copied project code (main.c & CMakeLists.txt)</i> from 
https://www.digikey.com/en/maker/projects/raspberry-pi-pico-and-rp2040-cc-part-1-blink-and-vs-code/7102fb8bca95452e9df6150f39ae8422

<h2> Summary to get vscode debugger working:</h2>

- Build a picoprobe
- install OpenOCD
- configure VSCode

<h2>Build Picoprobe</h2>
Download picoprobe.uf2 from this page: 

https://github.com/raspberrypi/picoprobe/releases/latest/download/picoprobe.uf2

Also relevant: 

https://github.com/raspberrypi/picoprobe/releases/tag/picoprobe-cmsis-v1.0.1

Hold down the bootsel button on the pico that you want to be a debugger, and drag the uf2 onto it. Hook up wires correctly, including (maybe) the SWD ground. 

<h2>Install OpenOCD</h2>
Use the tutorial located here:

https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf

Search for "picoprobe". Find the link to **Appendix A: Using Picoprobe** in the ToC, then Build OpenOCD ... Mac. 

I installed in the same directory as the pico-sdk. 

Then I tested OpenOCD, as the manual suggests. It ran from the command line. 

```
$ src/openocd
```

produced

```
Open On-Chip Debugger 0.11.0-g4f2ae61 (2023-04-23-12:06)
Licensed under GNU GPL v2
For bug reports, read
    http://openocd.org/doc/doxygen/bugs.html
embedded:startup.tcl:26: Error: Can't find openocd.cfg
in procedure 'script'
at file "embedded:startup.tcl", line 26
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Error: Debug Adapter has to be specified, see "adapter driver" command
embedded:startup.tcl:26: Error:
in procedure 'script'
at file "embedded:startup.tcl", line 26
```

After `make -j4` of openocd I also ran `make install`

so that I could just `$ openocd` to run it. People warn against doing this incase you run other instances. 

Further test that its working using 

```
$ src/openocd -f interface/cmsis-dap.cfg -c "adapter speed 5000" -f target/rp2040.cfg -s tcl
```

or 
```
$ openocd -f interface/cmsis-dap.cfg -c "adapter speed 5000" -f target/rp2040.cfg -s tcl
```

should yield

```
Open On-Chip Debugger 0.11.0-g4f2ae61 (2023-04-23-12:06)
Licensed under GNU GPL v2
For bug reports, read
    http://openocd.org/doc/doxygen/bugs.html
adapter speed: 5000 kHz

Info : auto-selecting first available session transport "swd". To override use 'transport select <transport>'.
Info : Hardware thread awareness created
Info : Hardware thread awareness created
Info : RP2040 Flash Bank Command
Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections
Info : Using CMSIS-DAPv2 interface with VID:PID=0x2e8a:0x000c, serial=E660D05113655332
Info : CMSIS-DAP: SWD  Supported
Info : CMSIS-DAP: FW Version = 2.0.0
Info : CMSIS-DAP: Interface Initialised (SWD)
Info : SWCLK/TCK = 0 SWDIO/TMS = 0 TDI = 0 TDO = 0 nTRST = 0 nRESET = 0
Info : CMSIS-DAP: Interface ready
Info : clock speed 5000 kHz
Info : SWD DPIDR 0x0bc12477
Info : SWD DLPIDR 0x00000001
Info : SWD DPIDR 0x0bc12477
Info : SWD DLPIDR 0x10000001
Info : rp2040.core0: hardware has 4 breakpoints, 2 watchpoints
Info : rp2040.core1: hardware has 4 breakpoints, 2 watchpoints
Info : starting gdb server for rp2040.core0 on 3333
Info : Listening on port 3333 for gdb connections
```

<h2>Configure VSCode</h2>

install cortex-debug extension

This link provides more info:

https://github.com/raspberrypi/pico-examples/tree/master/ide/vscode

The problem with the example of the launch.json file given in getting started guide is that it is for running SWD from a raspberry pi directly. The newer picoprobe.uf2 release works with `cmsis-dap.cfg` in the launch.json file.

I also had trouble with VSCode permissions, so a little help from 

https://forums.raspberrypi.com/viewtopic.php?t=306313

got me to the launch.json file - that works - that is included in this repo.



<h2>Other things I did which I don't think are necessary (but just in-case)</h2>

- Followed this article to install GDB. I dont think this was actually nessary for remote debugging, but I did make other progress afterwards. 
https://dev.to/jasonelwood/setup-gdb-on-macos-in-2020-489k
