Yell - Virtual sound card for Microsoft Windows guest in KVM
--------------------------------------------------------------- Yell is based
on [Scream](https://github.com/duncanthrax/scream/releases), which is a virtual
network sound card for Windows. Scream is an excellent way to stream system
audio from KVM windows guests to linux host, as qemu emulated sound cards do not
work well in Windows 10, suffering from audio clicking and interrupt problems.
However, when the guest network is bridged, scream can waste network bandwidth. 
Besides, scream cannot stream microphone input from host back to guest.

Yell is a virtual sound card working over emulated serial port and unix socket
to relieve pressure from network stack and provide duplex audio for Windows
guests.


Download and install
--------------------------------------------------------------- 
A ZIP file containing a signed x64 build is [available on the GitHub releases
page] The "installer" is a batch file that needs to be run with administrator
rights.

The build is supposed to run on all x64 versions of Windows 7 through Windows
10. 

Usage
--------------------------------------------------------------- 
All audio played through the Scream device will be put onto the local LAN as a
multicast stream. The multicast target address and port is always
"239.255.77.77:4010".  The audio is a raw PCM stream, always 44100kHz, 16bit,
stereo. It is transferred in UDP frames with a payload size of max. 980 bytes,
representing 1/180 second of audio. Delay is minimal, since all processing is
done on kernel level.

Receivers simply need to read the stream off the network and stuff it into a
local audio sink. The receiver system's kernel should automatically do the
necessary IGMP signalling, so it's usually sufficient to just open a multicast
listen socket and start reading from it. Minimal buffering (~ 4 times the UDP
payload size) should be done to account for jitter.

Three receivers are provided: 

- Linux/Pulseaudio: Not included in the installer package. Just type 'make' to
  build it.

- Linux/ALSA: Contributed by @ivan. Not included in the installer package. Just
  type 'make' to build it.

- Windows: ScreamReader, contributed by @MrShoenel. Included in the installer
  package as of version 1.2.

All three receivers can be run as unprivileged users.


Building
-------------------------------------------------------------
VS2015 and matching WDK are required. You might also have luck with earlier (or
future) VS versions, but I didn't test that.
