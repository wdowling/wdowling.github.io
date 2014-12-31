---
layout: post
title: OpenBSD on a MacBook Pro
---
After using my Macbook Pro 7,1 (mid-2010) for several years, it began to show
the usual signs of wear and tear. Boot up was already over a minute long to
reach the login screen and even after logging in the load time to generate
a usable desktop was laughable. Starting applications would take on average
between 15 - 20 seconds each time. OWC came to the rescue with an offer to
increase RAM to 8GB and swapping the SATA drive for an SSD.  This change made a
huge difference. Startup time is down to 10seconds and as soon as I am logged in
there is no longer the lag in opening any application. I highly recommend a
hardware upgrade!

Since I had 480GB of SSD to play with I decided to try to dualboot another OS. I
have been interested in moving away from Linux towards something more UNIX-like
but the opportunity never came about until now.

I had tested the various illumos based distributions on VirtualBox -(OpenIndiana
and OmniOS), and although it was great to see a Solaris kernel based
distribution alive (almost re: OpenIndiana), the lack of hardware support and
the reliance on Solaris closed binaries was not to my liking. I wanted an OS
that did not have the proprietry baggage of a previous history. So after a quick
scope of the land I decided to try out OpenBSD. I had not used it in many years
and it looked I was attempting to install it on hardware with limited support
but that is all part of the fun. It would also give me an excuse to follow the
mailing lists and learn more about UNIX internals.

#### Pre-Installation

Since I liked using OSX, I wanted to dual-boot both operating systems. Dual
booting can be achieved using the rEFInd Boot Manager -
http://www.rodsbooks.com/refind/. Apple hardware uses EFI instead of the
traditional BIOS for booting. The installation on OSX is simple:

`$ unzip refind-bin-0.8.4.zip`

`$ cd refind-bin-0.8.4`

`$ sudo ./install.sh`

This will create an efi directory on  / which you should not need to touch. 

It would be a good idea to install any OSX updates needed and do a full backup
with Time Machine or a cloning method of your choice. 

#### Installation and Configuration

Download install56.fs from your nearest OpenBSD mirror and dd it to a USB drive:

`# diskutil unmountDisk /dev/disk1` 

`# dd if=./install56.fs of=./dev/rdisk1 bs=1m` 

`# reboot`

Once the rEFInd menu loads you should be able to see the Puffy logo alongside
the Apple. Select the Puffy and hit enter. 

The first thing to do is to break out to the shell prompt to set the disk type.

`# fdisk -e wd0`

Change the type of the partition for OpenBSD to A6. Quit fdisk and proceed
to partition the disks as needed using disklabel.

`# disklabel -E wd0`

Once the disks are partitioned continue by typing 'install' and going
through the install process. Once the partition layout process appears choose
Custom Layout and you should be able to see the partitions you already created.
Make sure to assign mount points and save. Use sd0 for package locations.
 
#### Post-Installation

On reboot OpenBSD comes up without too much of an issue. The WiFi card is not
supported so an enternet cable was used instead. The first thing to do is to install some
utilities to get a basic desktop up and running.

`# echo "PKG_PATH=ftp://ftp.heanet.ie/pub/OpenBSD/5.6/`machine -a`/" >>
~/.profile` 

`# echo "export PKG_PATH" >> ~/.profile` 

`# pkg_add -i -v  vim mutt xfce` 

`$ echo 'exec startxfce4' >> .xinitrc`

Running `startx` starts up XFCE and gives you a taste of an OpenBSD desktop.

#### Thoughts

So what works?

Installing OpenBSD on Apple hardware is never going to be easy. This generation
of hardware does not make it any easier. The biggest issues are the NVidia
Geforce 320M graphics card and the Broadcom BCM4322 wireless card. Neither
provide good support and documentation for creating Open Source drivers. The
Xenocara project attempts to cover most NVidia cards with the 'nv' driver but
unfortunately this particular card is not supported. There is currently no sign
of Broadcom support in the near future so to get around this I used a mini
Edimax WiFi USB adapter using the urtwn driver. This works without any problems.

The keyboard still is not working properly but I have yet to investigate it further.
Backlighting, volume, screen brightness and so on are missing. Power management is
poor as I seem to only get 2 hours of battery life along with a very hot surface.

I'll continue to work on it and configure as much as possible. I hope to post more
details on the NVidia and Broadcom situation in the future.

And finally my dmesg output for good measure:


OpenBSD 5.6 (GENERIC.MP) #333: Fri Aug  8 00:20:21 MDT 2014
    deraadt@amd64.openbsd.org:/usr/src/sys/arch/amd64/compile/GENERIC.MP
RTC BIOS diagnostic error 77<ROM_cksum,config_unit,memory_size,invalid_time>
real mem = 8295346176 (7911MB)
avail mem = 8065740800 (7692MB)
mpath0 at root
scsibus0 at mpath0: 256 targets
mainbus0 at root
bios0 at mainbus0: SMBIOS rev. 2.4 @ 0xe0000 (44 entries)
bios0: vendor Apple Inc. version "MBP71.88Z.0039.B0E.1111071400" date 11/07/11
bios0: Apple Inc. MacBookPro7,1
acpi0 at bios0: rev 2
acpi0: sleep states S0 S3 S4 S5
acpi0: tables DSDT FACP HPET APIC APIC ASF! SBST ECDT SSDT SSDT MCFG SSDT SSDT
acpi0: wakeup devices ADP1(S3) LID0(S3) EC__(S3) OHC1(S3) EHC1(S3) OHC2(S3) EHC2(S3) ARPT(S5) GIGE(S5)
acpitimer0 at acpi0: 3579545 Hz, 24 bits
acpihpet0 at acpi0: 25000000 Hz
acpimadt0 at acpi0 addr 0xfee00000: PC-AT compat
cpu0 at mainbus0: apid 0 (boot processor)
cpu0: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.65 MHz
cpu0: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu0: 3MB 64b/line 8-way L2 cache
cpu0: smt 0, core 0, package 0
mtrr: Pentium Pro MTRR support, 8 var ranges, 88 fixed ranges
cpu0: apic clock running at 265MHz
cpu0: mwait min=64, max=64, C-substates=0.2.2.2.2, IBE
cpu1 at mainbus0: apid 1 (application processor)
cpu1: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.24 MHz
cpu1: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu1: 3MB 64b/line 8-way L2 cache
cpu1: smt 0, core 1, package 0
ioapic0 at mainbus0: apid 1 pa 0xfec00000, version 11, 24 pins
ioapic0: misconfigured as apic 0, remapped to apid 1
acpiec0 at acpi0
acpimcfg0 at acpi0 addr 0xf0000000, bus 0-4
acpiprt0 at acpi0: bus 0 (PCI0)
acpiprt1 at acpi0: bus 4 (IXVE)
acpicpu0 at acpi0: C3, C2, C1, PSS
acpicpu1 at acpi0: C3, C2, C1, PSS
acpiac0 at acpi0: AC unit offline
acpibtn0 at acpi0: LID0
acpibtn1 at acpi0: PWRB
acpibtn2 at acpi0: SLPB
acpibat0 at acpi0: BAT0 model "3545797981023400290" type 3545797981528607052 oem "3545797981528673619"
cpu0: Enhanced SpeedStep 2389 MHz: speeds: 2394, 2128, 1862, 1596, 798 MHz
memory map conflict 0xffc00000/0x400000
pci0 at mainbus0 bus 0
0:3:4: mem address conflict 0xd3400000/0x80000
pchb0 at pci0 dev 0 function 0 "NVIDIA MCP89 Host" rev 0xa1
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 0 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6d (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 0 not configured
vendor "NVIDIA", unknown product 0x0d6e (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6f (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 2 not configured
vendor "NVIDIA", unknown product 0x0d70 (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 3 not configured
vendor "NVIDIA", unknown product 0x0d71 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 0 not configured
vendor "NVIDIA", unknown product 0x0d72 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 1 not configured
pcib0 at pci0 dev 3 function 0 "NVIDIA MCP89 LPC" rev 0xa2
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 1 not configured
nviic0 at pci0 dev 3 function 2 "NVIDIA MCP89 SMBus" rev 0xa1
iic0 at nviic0
iic0: addr 0x2c 03=fc 05=67 06=a0 71=06 72=80 86=89 90=32 91=22 92=25 93=3d 94=62 95=3c 96=73 97=96 98=fd 9f=7c a0=7f a1=b5 a2=af a3=7b a4=28 a5=cf a6=64 a7=2d words 00=0000 01=0000 02=00fc 03=fc00 04=0067 05=67a0 06=a000 07=0000
spdmem0 at iic0 addr 0x50: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
spdmem1 at iic0 addr 0x51: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
iic1 at nviic0
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 3 not configured
"NVIDIA MCP89 Co-processor" rev 0xa1 at pci0 dev 3 function 4 not configured
ohci0 at pci0 dev 4 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 11, version 1.0, legacy support
ehci0 at pci0 dev 4 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 10
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
ohci1 at pci0 dev 6 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 7, version 1.0, legacy support
ehci1 at pci0 dev 6 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 5
usb1 at ehci1: USB revision 2.0
uhub1 at usb1 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
azalia0 at pci0 dev 8 function 0 "NVIDIA MCP89 HD Audio" rev 0xa2: msi
azalia0: codecs: Cirrus Logic CS4206, NVIDIA/0x000c, NVIDIA/0x000c, NVIDIA/0x000c, using Cirrus Logic CS4206
audio0 at azalia0
pciide0 at pci0 dev 10 function 0 "NVIDIA MCP89 SATA" rev 0xa2: DMA
pciide0: using apic 1 int 11 for native-PCI interrupt
wd0 at pciide0 channel 0 drive 0: <OWC Mercury Electra 3G SSD>
wd0: 1-sector PIO, LBA48, 457862MB, 937703088 sectors
wd0(pciide0:0:0): using PIO mode 4, Ultra-DMA mode 6
atapiscsi0 at pciide0 channel 1 drive 0
scsibus1 at atapiscsi0: 2 targets
cd0 at scsibus1 targ 0 lun 0: <MATSHITA, DVD-R UJ-898, HC10> ATAPI 5/cdrom removable
cd0(pciide0:1:0): using PIO mode 4, Ultra-DMA mode 5
vendor "NVIDIA", unknown product 0x0d75 (class memory subclass RAM, rev 0xa1) at pci0 dev 11 function 0 not configured
ppb0 at pci0 dev 14 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci1 at ppb0 bus 1
"AT&T/Lucent FW643 1394" rev 0x08 at pci1 dev 0 function 0 not configured
ppb1 at pci0 dev 21 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci2 at ppb1 bus 2
"Broadcom BCM4322" rev 0x01 at pci2 dev 0 function 0 not configured
ppb2 at pci0 dev 22 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci3 at ppb2 bus 3
bge0 at pci3 dev 0 function 0 "Broadcom BCM5764" rev 0x10, BCM5784 A1 (0x5784100): msi, address c8:bc:c8:93:db:19
brgphy0 at bge0 phy 1: BCM5784 10/100/1000baseT PHY, rev. 4
ppb3 at pci0 dev 23 function 0 "NVIDIA MCP89 PCIE" rev 0xa1
pci4 at ppb3 bus 4
vga1 at pci4 dev 0 function 0 "NVIDIA GeForce 320M" rev 0xa2
wsdisplay0 at vga1 mux 1: console (80x25, vt100 emulation)
wsdisplay0: screen 1-5 added (80x25, vt100 emulation)
isa0 at pcib0
isadma0 at isa0
pcppi0 at isa0 port 0x61
spkr0 at pcppi0
usb2 at ohci0: USB revision 1.0
uhub2 at usb2 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
usb3 at ohci1: USB revision 1.0
uhub3 at usb3 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
uvideo0 at uhub0 port 6 configuration 1 interface 0 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
video0 at uvideo0
ugen0 at uhub0 port 6 configuration 1 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
umass0 at uhub1 port 1 configuration 1 interface 0 "Apple Card Reader" rev 2.00/98.33 addr 2
umass0: using SCSI over Bulk-Only
scsibus2 at umass0: 2 targets, initiator 0
sd0 at scsibus2 targ 1 lun 0: <APPLE, SD Card Reader, 1.00> SCSI0 0/direct removable serial.05ac8403000000009833
urtwn0 at uhub1 port 4 "Realtek 802.11n WLAN Adapter" rev 2.00/2.00 addr 3
urtwn0: MAC/BB RTL8188CUS, RF 6052 1T1R, address 80:1f:02:ab:b4:3f
uhidev0 at uhub3 port 3 configuration 1 interface 0 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev0: iclass 3/1, 9 report ids
ukbd0 at uhidev0 reportid 1: 8 variable keys, 6 key codes, country code 13
wskbd0 at ukbd0: console keyboard, using wsdisplay0
uhid0 at uhidev0 reportid 9: input=0, output=0, feature=3
uhidev1 at uhub3 port 3 configuration 1 interface 1 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev1: iclass 3/0, 68 report ids
uhid1 at uhidev1 reportid 68: input=511, output=0, feature=0
ubcmtp0 at uhub3 port 3 configuration 1 interface 2 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
wsmouse0 at ubcmtp0 mux 0
uhidev2 at uhub3 port 5 configuration 1 interface 0 "Apple Computer, Inc. IR Receiver" rev 2.00/0.16 addr 3
uhidev2: iclass 3/0, 38 report ids
uhid2 at uhidev2 reportid 36: input=4, output=0, feature=0
uhid3 at uhidev2 reportid 37: input=4, output=0, feature=0
uhid4 at uhidev2 reportid 38: input=4, output=0, feature=0
uhub4 at uhub3 port 6 "Apple Inc. BRCM2046 Hub" rev 2.00/1.00 addr 4
ugen1 at uhub4 port 1 "Apple Inc. Bluetooth USB Host Controller" rev 2.00/2.07 addr 5
uhidev3 at uhub4 port 2 configuration 1 interface 0 "Apple Computer product 0x820a" rev 2.00/1.00 addr 6
uhidev3: iclass 3/1, 1 report id
ukbd1 at uhidev3 reportid 1: 8 variable keys, 6 key codes
wskbd1 at ukbd1 mux 1
wskbd1: connecting to wsdisplay0
uhidev4 at uhub4 port 3 configuration 1 interface 0 "Apple Computer product 0x820b" rev 2.00/1.00 addr 7
uhidev4: iclass 3/1, 2 report ids
ums0 at uhidev4 reportid 2: 3 buttons
wsmouse1 at ums0 mux 0
vscsi0 at root
scsibus3 at vscsi0: 256 targets
softraid0 at root
scsibus4 at softraid0: 256 targets
root on wd0a (3c22844f2527623e.a) swap on wd0b dump on wd0b
syncing disks... 
OpenBSD 5.6 (GENERIC.MP) #333: Fri Aug  8 00:20:21 MDT 2014
    deraadt@amd64.openbsd.org:/usr/src/sys/arch/amd64/compile/GENERIC.MP
RTC BIOS diagnostic error 77<ROM_cksum,config_unit,memory_size,invalid_time>
real mem = 8295346176 (7911MB)
avail mem = 8065740800 (7692MB)
mpath0 at root
scsibus0 at mpath0: 256 targets
mainbus0 at root
bios0 at mainbus0: SMBIOS rev. 2.4 @ 0xe0000 (44 entries)
bios0: vendor Apple Inc. version "MBP71.88Z.0039.B0E.1111071400" date 11/07/11
bios0: Apple Inc. MacBookPro7,1
acpi0 at bios0: rev 2
acpi0: sleep states S0 S3 S4 S5
acpi0: tables DSDT FACP HPET APIC APIC ASF! SBST ECDT SSDT SSDT MCFG SSDT SSDT
acpi0: wakeup devices ADP1(S3) LID0(S3) EC__(S3) OHC1(S3) EHC1(S3) OHC2(S3) EHC2(S3) ARPT(S5) GIGE(S5)
acpitimer0 at acpi0: 3579545 Hz, 24 bits
acpihpet0 at acpi0: 25000000 Hz
acpimadt0 at acpi0 addr 0xfee00000: PC-AT compat
cpu0 at mainbus0: apid 0 (boot processor)
cpu0: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.66 MHz
cpu0: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu0: 3MB 64b/line 8-way L2 cache
cpu0: smt 0, core 0, package 0
mtrr: Pentium Pro MTRR support, 8 var ranges, 88 fixed ranges
cpu0: apic clock running at 265MHz
cpu0: mwait min=64, max=64, C-substates=0.2.2.2.2, IBE
cpu1 at mainbus0: apid 1 (application processor)
cpu1: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.25 MHz
cpu1: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu1: 3MB 64b/line 8-way L2 cache
cpu1: smt 0, core 1, package 0
ioapic0 at mainbus0: apid 1 pa 0xfec00000, version 11, 24 pins
ioapic0: misconfigured as apic 0, remapped to apid 1
acpiec0 at acpi0
acpimcfg0 at acpi0 addr 0xf0000000, bus 0-4
acpiprt0 at acpi0: bus 0 (PCI0)
acpiprt1 at acpi0: bus 4 (IXVE)
acpicpu0 at acpi0: C3, C2, C1, PSS
acpicpu1 at acpi0: C3, C2, C1, PSS
acpiac0 at acpi0: AC unit online
acpibtn0 at acpi0: LID0
acpibtn1 at acpi0: PWRB
acpibtn2 at acpi0: SLPB
acpibat0 at acpi0: BAT0 model "3545797981023400290" type 3545797981528607052 oem "3545797981528673619"
cpu0: Enhanced SpeedStep 2389 MHz: speeds: 2394, 2128, 1862, 1596, 798 MHz
memory map conflict 0xffc00000/0x400000
pci0 at mainbus0 bus 0
0:3:4: mem address conflict 0xd3400000/0x80000
pchb0 at pci0 dev 0 function 0 "NVIDIA MCP89 Host" rev 0xa1
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 0 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6d (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 0 not configured
vendor "NVIDIA", unknown product 0x0d6e (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6f (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 2 not configured
vendor "NVIDIA", unknown product 0x0d70 (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 3 not configured
vendor "NVIDIA", unknown product 0x0d71 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 0 not configured
vendor "NVIDIA", unknown product 0x0d72 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 1 not configured
pcib0 at pci0 dev 3 function 0 "NVIDIA MCP89 LPC" rev 0xa2
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 1 not configured
nviic0 at pci0 dev 3 function 2 "NVIDIA MCP89 SMBus" rev 0xa1
iic0 at nviic0
iic0: addr 0x2c 03=fc 05=70 06=e0 71=06 72=80 86=89 90=32 91=22 92=25 93=3d 94=62 95=3c 96=73 97=96 98=fd 9f=7c a0=7f a1=b5 a2=af a3=7b a4=28 a5=cf a6=64 a7=2d words 00=0000 01=0000 02=00fc 03=fc00 04=0071 05=7100 06=0000 07=0000
spdmem0 at iic0 addr 0x50: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
spdmem1 at iic0 addr 0x51: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
iic1 at nviic0
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 3 not configured
"NVIDIA MCP89 Co-processor" rev 0xa1 at pci0 dev 3 function 4 not configured
ohci0 at pci0 dev 4 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 11, version 1.0, legacy support
ehci0 at pci0 dev 4 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 10
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
ohci1 at pci0 dev 6 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 7, version 1.0, legacy support
ehci1 at pci0 dev 6 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 5
usb1 at ehci1: USB revision 2.0
uhub1 at usb1 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
azalia0 at pci0 dev 8 function 0 "NVIDIA MCP89 HD Audio" rev 0xa2: msi
azalia0: codecs: Cirrus Logic CS4206, NVIDIA/0x000c, NVIDIA/0x000c, NVIDIA/0x000c, using Cirrus Logic CS4206
audio0 at azalia0
pciide0 at pci0 dev 10 function 0 "NVIDIA MCP89 SATA" rev 0xa2: DMA
pciide0: using apic 1 int 11 for native-PCI interrupt
wd0 at pciide0 channel 0 drive 0: <OWC Mercury Electra 3G SSD>
wd0: 1-sector PIO, LBA48, 457862MB, 937703088 sectors
wd0(pciide0:0:0): using PIO mode 4, Ultra-DMA mode 6
atapiscsi0 at pciide0 channel 1 drive 0
scsibus1 at atapiscsi0: 2 targets
cd0 at scsibus1 targ 0 lun 0: <MATSHITA, DVD-R UJ-898, HC10> ATAPI 5/cdrom removable
cd0(pciide0:1:0): using PIO mode 4, Ultra-DMA mode 5
vendor "NVIDIA", unknown product 0x0d75 (class memory subclass RAM, rev 0xa1) at pci0 dev 11 function 0 not configured
ppb0 at pci0 dev 14 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci1 at ppb0 bus 1
"AT&T/Lucent FW643 1394" rev 0x08 at pci1 dev 0 function 0 not configured
ppb1 at pci0 dev 21 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci2 at ppb1 bus 2
"Broadcom BCM4322" rev 0x01 at pci2 dev 0 function 0 not configured
ppb2 at pci0 dev 22 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci3 at ppb2 bus 3
bge0 at pci3 dev 0 function 0 "Broadcom BCM5764" rev 0x10, BCM5784 A1 (0x5784100): msi, address c8:bc:c8:93:db:19
brgphy0 at bge0 phy 1: BCM5784 10/100/1000baseT PHY, rev. 4
ppb3 at pci0 dev 23 function 0 "NVIDIA MCP89 PCIE" rev 0xa1
pci4 at ppb3 bus 4
vga1 at pci4 dev 0 function 0 "NVIDIA GeForce 320M" rev 0xa2
wsdisplay0 at vga1 mux 1: console (80x25, vt100 emulation)
wsdisplay0: screen 1-5 added (80x25, vt100 emulation)
isa0 at pcib0
isadma0 at isa0
pcppi0 at isa0 port 0x61
spkr0 at pcppi0
usb2 at ohci0: USB revision 1.0
uhub2 at usb2 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
usb3 at ohci1: USB revision 1.0
uhub3 at usb3 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
uvideo0 at uhub0 port 6 configuration 1 interface 0 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
video0 at uvideo0
ugen0 at uhub0 port 6 configuration 1 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
umass0 at uhub1 port 1 configuration 1 interface 0 "Apple Card Reader" rev 2.00/98.33 addr 2
umass0: using SCSI over Bulk-Only
scsibus2 at umass0: 2 targets, initiator 0
sd0 at scsibus2 targ 1 lun 0: <APPLE, SD Card Reader, 1.00> SCSI0 0/direct removable serial.05ac8403000000009833
urtwn0 at uhub1 port 4 "Realtek 802.11n WLAN Adapter" rev 2.00/2.00 addr 3
urtwn0: MAC/BB RTL8188CUS, RF 6052 1T1R, address 80:1f:02:ab:b4:3f
uhidev0 at uhub3 port 3 configuration 1 interface 0 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev0: iclass 3/1, 9 report ids
ukbd0 at uhidev0 reportid 1: 8 variable keys, 6 key codes, country code 13
wskbd0 at ukbd0: console keyboard, using wsdisplay0
uhid0 at uhidev0 reportid 9: input=0, output=0, feature=3
uhidev1 at uhub3 port 3 configuration 1 interface 1 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev1: iclass 3/0, 68 report ids
uhid1 at uhidev1 reportid 68: input=511, output=0, feature=0
ubcmtp0 at uhub3 port 3 configuration 1 interface 2 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
wsmouse0 at ubcmtp0 mux 0
uhidev2 at uhub3 port 5 configuration 1 interface 0 "Apple Computer, Inc. IR Receiver" rev 2.00/0.16 addr 3
uhidev2: iclass 3/0, 38 report ids
uhid2 at uhidev2 reportid 36: input=4, output=0, feature=0
uhid3 at uhidev2 reportid 37: input=4, output=0, feature=0
uhid4 at uhidev2 reportid 38: input=4, output=0, feature=0
uhub4 at uhub3 port 6 "Apple Inc. BRCM2046 Hub" rev 2.00/1.00 addr 4
ugen1 at uhub4 port 1 "Apple Inc. Bluetooth USB Host Controller" rev 2.00/2.07 addr 5
uhidev3 at uhub4 port 2 configuration 1 interface 0 "Apple Computer product 0x820a" rev 2.00/1.00 addr 6
uhidev3: iclass 3/1, 1 report id
ukbd1 at uhidev3 reportid 1: 8 variable keys, 6 key codes
wskbd1 at ukbd1 mux 1
wskbd1: connecting to wsdisplay0
uhidev4 at uhub4 port 3 configuration 1 interface 0 "Apple Computer product 0x820b" rev 2.00/1.00 addr 7
uhidev4: iclass 3/1, 2 report ids
ums0 at uhidev4 reportid 2: 3 buttons
wsmouse1 at ums0 mux 0
vscsi0 at root
scsibus3 at vscsi0: 256 targets
softraid0 at root
scsibus4 at softraid0: 256 targets
root on wd0a (3c22844f2527623e.a) swap on wd0b dump on wd0b
syncing disks... 
OpenBSD 5.6 (GENERIC.MP) #333: Fri Aug  8 00:20:21 MDT 2014
    deraadt@amd64.openbsd.org:/usr/src/sys/arch/amd64/compile/GENERIC.MP
RTC BIOS diagnostic error 77<ROM_cksum,config_unit,memory_size,invalid_time>
real mem = 8295346176 (7911MB)
avail mem = 8065740800 (7692MB)
mpath0 at root
scsibus0 at mpath0: 256 targets
mainbus0 at root
bios0 at mainbus0: SMBIOS rev. 2.4 @ 0xe0000 (44 entries)
bios0: vendor Apple Inc. version "MBP71.88Z.0039.B0E.1111071400" date 11/07/11
bios0: Apple Inc. MacBookPro7,1
acpi0 at bios0: rev 2
acpi0: sleep states S0 S3 S4 S5
acpi0: tables DSDT FACP HPET APIC APIC ASF! SBST ECDT SSDT SSDT MCFG SSDT SSDT
acpi0: wakeup devices ADP1(S3) LID0(S3) EC__(S3) OHC1(S3) EHC1(S3) OHC2(S3) EHC2(S3) ARPT(S5) GIGE(S5)
acpitimer0 at acpi0: 3579545 Hz, 24 bits
acpihpet0 at acpi0: 25000000 Hz
acpimadt0 at acpi0 addr 0xfee00000: PC-AT compat
cpu0 at mainbus0: apid 0 (boot processor)
cpu0: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.56 MHz
cpu0: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu0: 3MB 64b/line 8-way L2 cache
cpu0: smt 0, core 0, package 0
mtrr: Pentium Pro MTRR support, 8 var ranges, 88 fixed ranges
cpu0: apic clock running at 265MHz
cpu0: mwait min=64, max=64, C-substates=0.2.2.2.2, IBE
cpu1 at mainbus0: apid 1 (application processor)
cpu1: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.24 MHz
cpu1: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu1: 3MB 64b/line 8-way L2 cache
cpu1: smt 0, core 1, package 0
ioapic0 at mainbus0: apid 1 pa 0xfec00000, version 11, 24 pins
ioapic0: misconfigured as apic 0, remapped to apid 1
acpiec0 at acpi0
acpimcfg0 at acpi0 addr 0xf0000000, bus 0-4
acpiprt0 at acpi0: bus 0 (PCI0)
acpiprt1 at acpi0: bus 4 (IXVE)
acpicpu0 at acpi0: C3, C2, C1, PSS
acpicpu1 at acpi0: C3, C2, C1, PSS
acpiac0 at acpi0: AC unit online
acpibtn0 at acpi0: LID0
acpibtn1 at acpi0: PWRB
acpibtn2 at acpi0: SLPB
acpibat0 at acpi0: BAT0 model "3545797981023400290" type 3545797981528607052 oem "3545797981528673619"
cpu0: Enhanced SpeedStep 2389 MHz: speeds: 2394, 2128, 1862, 1596, 798 MHz
memory map conflict 0xffc00000/0x400000
pci0 at mainbus0 bus 0
0:3:4: mem address conflict 0xd3400000/0x80000
pchb0 at pci0 dev 0 function 0 "NVIDIA MCP89 Host" rev 0xa1
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 0 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6d (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 0 not configured
vendor "NVIDIA", unknown product 0x0d6e (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6f (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 2 not configured
vendor "NVIDIA", unknown product 0x0d70 (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 3 not configured
vendor "NVIDIA", unknown product 0x0d71 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 0 not configured
vendor "NVIDIA", unknown product 0x0d72 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 1 not configured
pcib0 at pci0 dev 3 function 0 "NVIDIA MCP89 LPC" rev 0xa2
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 1 not configured
nviic0 at pci0 dev 3 function 2 "NVIDIA MCP89 SMBus" rev 0xa1
iic0 at nviic0
iic0: addr 0x2c 03=fc 05=70 06=e0 71=06 72=80 86=88 90=32 91=22 92=25 93=3d 94=62 95=3c 96=73 97=96 98=fd 9f=7c a0=7f a1=b5 a2=af a3=7b a4=28 a5=cf a6=64 a7=2d words 00=0000 01=0000 02=00fc 03=fc00 04=0071 05=7100 06=0000 07=0000
spdmem0 at iic0 addr 0x50: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
spdmem1 at iic0 addr 0x51: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
iic1 at nviic0
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 3 not configured
"NVIDIA MCP89 Co-processor" rev 0xa1 at pci0 dev 3 function 4 not configured
ohci0 at pci0 dev 4 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 11, version 1.0, legacy support
ehci0 at pci0 dev 4 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 10
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
ohci1 at pci0 dev 6 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 7, version 1.0, legacy support
ehci1 at pci0 dev 6 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 5
usb1 at ehci1: USB revision 2.0
uhub1 at usb1 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
azalia0 at pci0 dev 8 function 0 "NVIDIA MCP89 HD Audio" rev 0xa2: msi
azalia0: codecs: Cirrus Logic CS4206, NVIDIA/0x000c, NVIDIA/0x000c, NVIDIA/0x000c, using Cirrus Logic CS4206
audio0 at azalia0
pciide0 at pci0 dev 10 function 0 "NVIDIA MCP89 SATA" rev 0xa2: DMA
pciide0: using apic 1 int 11 for native-PCI interrupt
wd0 at pciide0 channel 0 drive 0: <OWC Mercury Electra 3G SSD>
wd0: 1-sector PIO, LBA48, 457862MB, 937703088 sectors
wd0(pciide0:0:0): using PIO mode 4, Ultra-DMA mode 6
atapiscsi0 at pciide0 channel 1 drive 0
scsibus1 at atapiscsi0: 2 targets
cd0 at scsibus1 targ 0 lun 0: <MATSHITA, DVD-R UJ-898, HC10> ATAPI 5/cdrom removable
cd0(pciide0:1:0): using PIO mode 4, Ultra-DMA mode 5
vendor "NVIDIA", unknown product 0x0d75 (class memory subclass RAM, rev 0xa1) at pci0 dev 11 function 0 not configured
ppb0 at pci0 dev 14 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci1 at ppb0 bus 1
"AT&T/Lucent FW643 1394" rev 0x08 at pci1 dev 0 function 0 not configured
ppb1 at pci0 dev 21 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci2 at ppb1 bus 2
"Broadcom BCM4322" rev 0x01 at pci2 dev 0 function 0 not configured
ppb2 at pci0 dev 22 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci3 at ppb2 bus 3
bge0 at pci3 dev 0 function 0 "Broadcom BCM5764" rev 0x10, BCM5784 A1 (0x5784100): msi, address c8:bc:c8:93:db:19
brgphy0 at bge0 phy 1: BCM5784 10/100/1000baseT PHY, rev. 4
ppb3 at pci0 dev 23 function 0 "NVIDIA MCP89 PCIE" rev 0xa1
pci4 at ppb3 bus 4
vga1 at pci4 dev 0 function 0 "NVIDIA GeForce 320M" rev 0xa2
wsdisplay0 at vga1 mux 1: console (80x25, vt100 emulation)
wsdisplay0: screen 1-5 added (80x25, vt100 emulation)
isa0 at pcib0
isadma0 at isa0
pcppi0 at isa0 port 0x61
spkr0 at pcppi0
usb2 at ohci0: USB revision 1.0
uhub2 at usb2 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
usb3 at ohci1: USB revision 1.0
uhub3 at usb3 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
uvideo0 at uhub0 port 6 configuration 1 interface 0 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
video0 at uvideo0
ugen0 at uhub0 port 6 configuration 1 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
umass0 at uhub1 port 1 configuration 1 interface 0 "Apple Card Reader" rev 2.00/98.33 addr 2
umass0: using SCSI over Bulk-Only
scsibus2 at umass0: 2 targets, initiator 0
sd0 at scsibus2 targ 1 lun 0: <APPLE, SD Card Reader, 1.00> SCSI0 0/direct removable serial.05ac8403000000009833
urtwn0 at uhub1 port 4 "Realtek 802.11n WLAN Adapter" rev 2.00/2.00 addr 3
urtwn0: MAC/BB RTL8188CUS, RF 6052 1T1R, address 80:1f:02:ab:b4:3f
uhidev0 at uhub3 port 3 configuration 1 interface 0 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev0: iclass 3/1, 9 report ids
ukbd0 at uhidev0 reportid 1: 8 variable keys, 6 key codes, country code 13
wskbd0 at ukbd0: console keyboard, using wsdisplay0
uhid0 at uhidev0 reportid 9: input=0, output=0, feature=3
uhidev1 at uhub3 port 3 configuration 1 interface 1 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev1: iclass 3/0, 68 report ids
uhid1 at uhidev1 reportid 68: input=511, output=0, feature=0
ubcmtp0 at uhub3 port 3 configuration 1 interface 2 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
wsmouse0 at ubcmtp0 mux 0
uhidev2 at uhub3 port 5 configuration 1 interface 0 "Apple Computer, Inc. IR Receiver" rev 2.00/0.16 addr 3
uhidev2: iclass 3/0, 38 report ids
uhid2 at uhidev2 reportid 36: input=4, output=0, feature=0
uhid3 at uhidev2 reportid 37: input=4, output=0, feature=0
uhid4 at uhidev2 reportid 38: input=4, output=0, feature=0
uhub4 at uhub3 port 6 "Apple Inc. BRCM2046 Hub" rev 2.00/1.00 addr 4
ugen1 at uhub4 port 1 "Apple Inc. Bluetooth USB Host Controller" rev 2.00/2.07 addr 5
uhidev3 at uhub4 port 2 configuration 1 interface 0 "Apple Computer product 0x820a" rev 2.00/1.00 addr 6
uhidev3: iclass 3/1, 1 report id
ukbd1 at uhidev3 reportid 1: 8 variable keys, 6 key codes
wskbd1 at ukbd1 mux 1
wskbd1: connecting to wsdisplay0
uhidev4 at uhub4 port 3 configuration 1 interface 0 "Apple Computer product 0x820b" rev 2.00/1.00 addr 7
uhidev4: iclass 3/1, 2 report ids
ums0 at uhidev4 reportid 2: 3 buttons
wsmouse1 at ums0 mux 0
vscsi0 at root
scsibus3 at vscsi0: 256 targets
softraid0 at root
scsibus4 at softraid0: 256 targets
root on wd0a (3c22844f2527623e.a) swap on wd0b dump on wd0b
syncing disks... 
OpenBSD 5.6 (GENERIC.MP) #333: Fri Aug  8 00:20:21 MDT 2014
    deraadt@amd64.openbsd.org:/usr/src/sys/arch/amd64/compile/GENERIC.MP
RTC BIOS diagnostic error 77<ROM_cksum,config_unit,memory_size,invalid_time>
real mem = 8295346176 (7911MB)
avail mem = 8065740800 (7692MB)
mpath0 at root
scsibus0 at mpath0: 256 targets
mainbus0 at root
bios0 at mainbus0: SMBIOS rev. 2.4 @ 0xe0000 (44 entries)
bios0: vendor Apple Inc. version "MBP71.88Z.0039.B0E.1111071400" date 11/07/11
bios0: Apple Inc. MacBookPro7,1
acpi0 at bios0: rev 2
acpi0: sleep states S0 S3 S4 S5
acpi0: tables DSDT FACP HPET APIC APIC ASF! SBST ECDT SSDT SSDT MCFG SSDT SSDT
acpi0: wakeup devices ADP1(S3) LID0(S3) EC__(S3) OHC1(S3) EHC1(S3) OHC2(S3) EHC2(S3) ARPT(S5) GIGE(S5)
acpitimer0 at acpi0: 3579545 Hz, 24 bits
acpihpet0 at acpi0: 25000000 Hz
acpimadt0 at acpi0 addr 0xfee00000: PC-AT compat
cpu0 at mainbus0: apid 0 (boot processor)
cpu0: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.66 MHz
cpu0: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu0: 3MB 64b/line 8-way L2 cache
cpu0: smt 0, core 0, package 0
mtrr: Pentium Pro MTRR support, 8 var ranges, 88 fixed ranges
cpu0: apic clock running at 265MHz
cpu0: mwait min=64, max=64, C-substates=0.2.2.2.2, IBE
cpu1 at mainbus0: apid 1 (application processor)
cpu1: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.25 MHz
cpu1: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu1: 3MB 64b/line 8-way L2 cache
cpu1: smt 0, core 1, package 0
ioapic0 at mainbus0: apid 1 pa 0xfec00000, version 11, 24 pins
ioapic0: misconfigured as apic 0, remapped to apid 1
acpiec0 at acpi0
acpimcfg0 at acpi0 addr 0xf0000000, bus 0-4
acpiprt0 at acpi0: bus 0 (PCI0)
acpiprt1 at acpi0: bus 4 (IXVE)
acpicpu0 at acpi0: C3, C2, C1, PSS
acpicpu1 at acpi0: C3, C2, C1, PSS
acpiac0 at acpi0: AC unit online
acpibtn0 at acpi0: LID0
acpibtn1 at acpi0: PWRB
acpibtn2 at acpi0: SLPB
acpibat0 at acpi0: BAT0 model "3545797981023400290" type 3545797981528607052 oem "3545797981528673619"
cpu0: Enhanced SpeedStep 2389 MHz: speeds: 2394, 2128, 1862, 1596, 798 MHz
memory map conflict 0xffc00000/0x400000
pci0 at mainbus0 bus 0
0:3:4: mem address conflict 0xd3400000/0x80000
pchb0 at pci0 dev 0 function 0 "NVIDIA MCP89 Host" rev 0xa1
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 0 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6d (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 0 not configured
vendor "NVIDIA", unknown product 0x0d6e (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6f (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 2 not configured
vendor "NVIDIA", unknown product 0x0d70 (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 3 not configured
vendor "NVIDIA", unknown product 0x0d71 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 0 not configured
vendor "NVIDIA", unknown product 0x0d72 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 1 not configured
pcib0 at pci0 dev 3 function 0 "NVIDIA MCP89 LPC" rev 0xa2
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 1 not configured
nviic0 at pci0 dev 3 function 2 "NVIDIA MCP89 SMBus" rev 0xa1
iic0 at nviic0
iic0: addr 0x2c 03=fc 05=71 71=06 72=80 86=88 90=32 91=22 92=25 93=3d 94=62 95=3c 96=73 97=96 98=fd 9f=7c a0=7f a1=b5 a2=af a3=7b a4=28 a5=cf a6=64 a7=2d words 00=0000 01=0000 02=00fc 03=fc00 04=0070 05=70c0 06=c000 07=0000
spdmem0 at iic0 addr 0x50: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
spdmem1 at iic0 addr 0x51: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
iic1 at nviic0
iic1: addr 0x4c 00=40 01=4d 02=80 04=07 05=55 07=55 0a=07 0b=55 0d=55 10=e0 15=55 19=55 1a=55 20=55 21=0a 22=70 23=3b 24=a0 25=0b 26=0f 27=12 28=12 29=e0 60=06 70=06 71=03 72=07 a4=8d a5=e0 ac=29 ad=92 ae=26 af=50 b0=20 b1=20 fd=21 fe=5d ff=03 words 00=40ff 01=4dff 02=80ff 03=00ff 04=07ff 05=55ff 06=00ff 07=55ff
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 3 not configured
"NVIDIA MCP89 Co-processor" rev 0xa1 at pci0 dev 3 function 4 not configured
ohci0 at pci0 dev 4 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 11, version 1.0, legacy support
ehci0 at pci0 dev 4 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 10
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
ohci1 at pci0 dev 6 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 7, version 1.0, legacy support
ehci1 at pci0 dev 6 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 5
usb1 at ehci1: USB revision 2.0
uhub1 at usb1 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
azalia0 at pci0 dev 8 function 0 "NVIDIA MCP89 HD Audio" rev 0xa2: msi
azalia0: codecs: Cirrus Logic CS4206, NVIDIA/0x000c, NVIDIA/0x000c, NVIDIA/0x000c, using Cirrus Logic CS4206
audio0 at azalia0
pciide0 at pci0 dev 10 function 0 "NVIDIA MCP89 SATA" rev 0xa2: DMA
pciide0: using apic 1 int 11 for native-PCI interrupt
wd0 at pciide0 channel 0 drive 0: <OWC Mercury Electra 3G SSD>
wd0: 1-sector PIO, LBA48, 457862MB, 937703088 sectors
wd0(pciide0:0:0): using PIO mode 4, Ultra-DMA mode 6
atapiscsi0 at pciide0 channel 1 drive 0
scsibus1 at atapiscsi0: 2 targets
cd0 at scsibus1 targ 0 lun 0: <MATSHITA, DVD-R UJ-898, HC10> ATAPI 5/cdrom removable
cd0(pciide0:1:0): using PIO mode 4, Ultra-DMA mode 5
vendor "NVIDIA", unknown product 0x0d75 (class memory subclass RAM, rev 0xa1) at pci0 dev 11 function 0 not configured
ppb0 at pci0 dev 14 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci1 at ppb0 bus 1
"AT&T/Lucent FW643 1394" rev 0x08 at pci1 dev 0 function 0 not configured
ppb1 at pci0 dev 21 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci2 at ppb1 bus 2
"Broadcom BCM4322" rev 0x01 at pci2 dev 0 function 0 not configured
ppb2 at pci0 dev 22 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci3 at ppb2 bus 3
bge0 at pci3 dev 0 function 0 "Broadcom BCM5764" rev 0x10, BCM5784 A1 (0x5784100): msi, address c8:bc:c8:93:db:19
brgphy0 at bge0 phy 1: BCM5784 10/100/1000baseT PHY, rev. 4
ppb3 at pci0 dev 23 function 0 "NVIDIA MCP89 PCIE" rev 0xa1
pci4 at ppb3 bus 4
vga1 at pci4 dev 0 function 0 "NVIDIA GeForce 320M" rev 0xa2
wsdisplay0 at vga1 mux 1: console (80x25, vt100 emulation)
wsdisplay0: screen 1-5 added (80x25, vt100 emulation)
isa0 at pcib0
isadma0 at isa0
pcppi0 at isa0 port 0x61
spkr0 at pcppi0
usb2 at ohci0: USB revision 1.0
uhub2 at usb2 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
usb3 at ohci1: USB revision 1.0
uhub3 at usb3 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
uvideo0 at uhub0 port 6 configuration 1 interface 0 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
video0 at uvideo0
ugen0 at uhub0 port 6 configuration 1 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
umass0 at uhub1 port 1 configuration 1 interface 0 "Apple Card Reader" rev 2.00/98.33 addr 2
umass0: using SCSI over Bulk-Only
scsibus2 at umass0: 2 targets, initiator 0
sd0 at scsibus2 targ 1 lun 0: <APPLE, SD Card Reader, 1.00> SCSI0 0/direct removable serial.05ac8403000000009833
urtwn0 at uhub1 port 4 "Realtek 802.11n WLAN Adapter" rev 2.00/2.00 addr 3
urtwn0: MAC/BB RTL8188CUS, RF 6052 1T1R, address 80:1f:02:ab:b4:3f
uhidev0 at uhub3 port 3 configuration 1 interface 0 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev0: iclass 3/1, 9 report ids
ukbd0 at uhidev0 reportid 1: 8 variable keys, 6 key codes, country code 13
wskbd0 at ukbd0: console keyboard, using wsdisplay0
uhid0 at uhidev0 reportid 9: input=0, output=0, feature=3
uhidev1 at uhub3 port 3 configuration 1 interface 1 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev1: iclass 3/0, 68 report ids
uhid1 at uhidev1 reportid 68: input=511, output=0, feature=0
ubcmtp0 at uhub3 port 3 configuration 1 interface 2 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
wsmouse0 at ubcmtp0 mux 0
uhidev2 at uhub3 port 5 configuration 1 interface 0 "Apple Computer, Inc. IR Receiver" rev 2.00/0.16 addr 3
uhidev2: iclass 3/0, 38 report ids
uhid2 at uhidev2 reportid 36: input=4, output=0, feature=0
uhid3 at uhidev2 reportid 37: input=4, output=0, feature=0
uhid4 at uhidev2 reportid 38: input=4, output=0, feature=0
uhub4 at uhub3 port 6 "Apple Inc. BRCM2046 Hub" rev 2.00/1.00 addr 4
ugen1 at uhub4 port 1 "Apple Inc. Bluetooth USB Host Controller" rev 2.00/2.07 addr 5
uhidev3 at uhub4 port 2 configuration 1 interface 0 "Apple Computer product 0x820a" rev 2.00/1.00 addr 6
uhidev3: iclass 3/1, 1 report id
ukbd1 at uhidev3 reportid 1: 8 variable keys, 6 key codes
wskbd1 at ukbd1 mux 1
wskbd1: connecting to wsdisplay0
uhidev4 at uhub4 port 3 configuration 1 interface 0 "Apple Computer product 0x820b" rev 2.00/1.00 addr 7
uhidev4: iclass 3/1, 2 report ids
ums0 at uhidev4 reportid 2: 3 buttons
wsmouse1 at ums0 mux 0
vscsi0 at root
scsibus3 at vscsi0: 256 targets
softraid0 at root
scsibus4 at softraid0: 256 targets
root on wd0a (3c22844f2527623e.a) swap on wd0b dump on wd0b
syncing disks... 
OpenBSD 5.6 (GENERIC.MP) #333: Fri Aug  8 00:20:21 MDT 2014
    deraadt@amd64.openbsd.org:/usr/src/sys/arch/amd64/compile/GENERIC.MP
RTC BIOS diagnostic error 77<ROM_cksum,config_unit,memory_size,invalid_time>
real mem = 8295346176 (7911MB)
avail mem = 8065740800 (7692MB)
mpath0 at root
scsibus0 at mpath0: 256 targets
mainbus0 at root
bios0 at mainbus0: SMBIOS rev. 2.4 @ 0xe0000 (44 entries)
bios0: vendor Apple Inc. version "MBP71.88Z.0039.B0E.1111071400" date 11/07/11
bios0: Apple Inc. MacBookPro7,1
acpi0 at bios0: rev 2
acpi0: sleep states S0 S3 S4 S5
acpi0: tables DSDT FACP HPET APIC APIC ASF! SBST ECDT SSDT SSDT MCFG SSDT SSDT
acpi0: wakeup devices ADP1(S3) LID0(S3) EC__(S3) OHC1(S3) EHC1(S3) OHC2(S3) EHC2(S3) ARPT(S5) GIGE(S5)
acpitimer0 at acpi0: 3579545 Hz, 24 bits
acpihpet0 at acpi0: 25000000 Hz
acpimadt0 at acpi0 addr 0xfee00000: PC-AT compat
cpu0 at mainbus0: apid 0 (boot processor)
cpu0: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.68 MHz
cpu0: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu0: 3MB 64b/line 8-way L2 cache
cpu0: smt 0, core 0, package 0
mtrr: Pentium Pro MTRR support, 8 var ranges, 88 fixed ranges
cpu0: apic clock running at 265MHz
cpu0: mwait min=64, max=64, C-substates=0.2.2.2.2, IBE
cpu1 at mainbus0: apid 1 (application processor)
cpu1: Intel(R) Core(TM)2 Duo CPU P8600 @ 2.40GHz, 2389.24 MHz
cpu1: FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CFLUSH,DS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE,SSE3,DTES64,MWAIT,DS-CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1,XSAVE,NXE,LONG,LAHF,PERF
cpu1: 3MB 64b/line 8-way L2 cache
cpu1: smt 0, core 1, package 0
ioapic0 at mainbus0: apid 1 pa 0xfec00000, version 11, 24 pins
ioapic0: misconfigured as apic 0, remapped to apid 1
acpiec0 at acpi0
acpimcfg0 at acpi0 addr 0xf0000000, bus 0-4
acpiprt0 at acpi0: bus 0 (PCI0)
acpiprt1 at acpi0: bus 4 (IXVE)
acpicpu0 at acpi0: C3, C2, C1, PSS
acpicpu1 at acpi0: C3, C2, C1, PSS
acpiac0 at acpi0: AC unit online
acpibtn0 at acpi0: LID0
acpibtn1 at acpi0: PWRB
acpibtn2 at acpi0: SLPB
acpibat0 at acpi0: BAT0 model "3545797981023400290" type 3545797981528607052 oem "3545797981528673619"
cpu0: Enhanced SpeedStep 2389 MHz: speeds: 2394, 2128, 1862, 1596, 798 MHz
memory map conflict 0xffc00000/0x400000
pci0 at mainbus0 bus 0
0:3:4: mem address conflict 0xd3400000/0x80000
pchb0 at pci0 dev 0 function 0 "NVIDIA MCP89 Host" rev 0xa1
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 0 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6d (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 0 not configured
vendor "NVIDIA", unknown product 0x0d6e (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 1 not configured
vendor "NVIDIA", unknown product 0x0d6f (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 2 not configured
vendor "NVIDIA", unknown product 0x0d70 (class memory subclass RAM, rev 0xa1) at pci0 dev 1 function 3 not configured
vendor "NVIDIA", unknown product 0x0d71 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 0 not configured
vendor "NVIDIA", unknown product 0x0d72 (class memory subclass RAM, rev 0xa1) at pci0 dev 2 function 1 not configured
pcib0 at pci0 dev 3 function 0 "NVIDIA MCP89 LPC" rev 0xa2
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 1 not configured
nviic0 at pci0 dev 3 function 2 "NVIDIA MCP89 SMBus" rev 0xa1
iic0 at nviic0
iic0: addr 0x2c 03=fc 05=70 06=e0 71=06 72=80 86=88 90=32 91=22 92=25 93=3d 94=62 95=3c 96=73 97=96 98=fd 9f=7c a0=7f a1=b5 a2=af a3=7b a4=28 a5=cf a6=64 a7=2d words 00=0000 01=0000 02=00fc 03=fc00 04=0071 05=70e0 06=e000 07=0000
spdmem0 at iic0 addr 0x50: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
spdmem1 at iic0 addr 0x51: 4GB DDR3 SDRAM PC3-8500 SO-DIMM
iic1 at nviic0
iic1: addr 0x4c 01=4c 02=80 03=00 04=07 05=55 06=00 07=55 08=00 09=00 0a=07 0b=55 0c=00 0d=55 0e=00 0f=00 10=a0 11=00 12=00 13=00 14=00 15=55 16=00 17=00 18=00 19=55 1a=55 1b=00 1c=00 1d=00 1e=00 1f=00 23=3a 24=c0 25=0b 26=0f 27=12 28=12 29=a0 2a=00 30=00 31=00 32=00 33=00 34=00 35=00 36=00 37=00 38=00 39=00 3a=00 3b=00 3c=00 3d=00 3e=00 3f=00 40=00 41=00 42=00 43=00 44=00 45=00 46=00 47=00 48=00 words 00=ffff 01=ffff 02=ffff 03=ffff 04=ffff 05=ffff 06=ffff 07=ffff
"NVIDIA MCP89 Memory" rev 0xa1 at pci0 dev 3 function 3 not configured
"NVIDIA MCP89 Co-processor" rev 0xa1 at pci0 dev 3 function 4 not configured
ohci0 at pci0 dev 4 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 11, version 1.0, legacy support
ehci0 at pci0 dev 4 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 10
usb0 at ehci0: USB revision 2.0
uhub0 at usb0 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
ohci1 at pci0 dev 6 function 0 "NVIDIA MCP89 USB" rev 0xa1: apic 1 int 7, version 1.0, legacy support
ehci1 at pci0 dev 6 function 1 "NVIDIA MCP89 USB" rev 0xa2: apic 1 int 5
usb1 at ehci1: USB revision 2.0
uhub1 at usb1 "NVIDIA EHCI root hub" rev 2.00/1.00 addr 1
azalia0 at pci0 dev 8 function 0 "NVIDIA MCP89 HD Audio" rev 0xa2: msi
azalia0: codecs: Cirrus Logic CS4206, NVIDIA/0x000c, NVIDIA/0x000c, NVIDIA/0x000c, using Cirrus Logic CS4206
audio0 at azalia0
pciide0 at pci0 dev 10 function 0 "NVIDIA MCP89 SATA" rev 0xa2: DMA
pciide0: using apic 1 int 11 for native-PCI interrupt
wd0 at pciide0 channel 0 drive 0: <OWC Mercury Electra 3G SSD>
wd0: 1-sector PIO, LBA48, 457862MB, 937703088 sectors
wd0(pciide0:0:0): using PIO mode 4, Ultra-DMA mode 6
atapiscsi0 at pciide0 channel 1 drive 0
scsibus1 at atapiscsi0: 2 targets
cd0 at scsibus1 targ 0 lun 0: <MATSHITA, DVD-R UJ-898, HC10> ATAPI 5/cdrom removable
cd0(pciide0:1:0): using PIO mode 4, Ultra-DMA mode 5
vendor "NVIDIA", unknown product 0x0d75 (class memory subclass RAM, rev 0xa1) at pci0 dev 11 function 0 not configured
ppb0 at pci0 dev 14 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci1 at ppb0 bus 1
"AT&T/Lucent FW643 1394" rev 0x08 at pci1 dev 0 function 0 not configured
ppb1 at pci0 dev 21 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci2 at ppb1 bus 2
"Broadcom BCM4322" rev 0x01 at pci2 dev 0 function 0 not configured
ppb2 at pci0 dev 22 function 0 "NVIDIA MCP89 PCIE" rev 0xa1: msi
pci3 at ppb2 bus 3
bge0 at pci3 dev 0 function 0 "Broadcom BCM5764" rev 0x10, BCM5784 A1 (0x5784100): msi, address c8:bc:c8:93:db:19
brgphy0 at bge0 phy 1: BCM5784 10/100/1000baseT PHY, rev. 4
ppb3 at pci0 dev 23 function 0 "NVIDIA MCP89 PCIE" rev 0xa1
pci4 at ppb3 bus 4
vga1 at pci4 dev 0 function 0 "NVIDIA GeForce 320M" rev 0xa2
wsdisplay0 at vga1 mux 1: console (80x25, vt100 emulation)
wsdisplay0: screen 1-5 added (80x25, vt100 emulation)
isa0 at pcib0
isadma0 at isa0
pcppi0 at isa0 port 0x61
spkr0 at pcppi0
usb2 at ohci0: USB revision 1.0
uhub2 at usb2 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
usb3 at ohci1: USB revision 1.0
uhub3 at usb3 "NVIDIA OHCI root hub" rev 1.00/1.00 addr 1
uvideo0 at uhub0 port 6 configuration 1 interface 0 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
video0 at uvideo0
ugen0 at uhub0 port 6 configuration 1 "Apple Inc. Built-in iSight" rev 2.00/4.35 addr 2
umass0 at uhub1 port 1 configuration 1 interface 0 "Apple Card Reader" rev 2.00/98.33 addr 2
umass0: using SCSI over Bulk-Only
scsibus2 at umass0: 2 targets, initiator 0
sd0 at scsibus2 targ 1 lun 0: <APPLE, SD Card Reader, 1.00> SCSI0 0/direct removable serial.05ac8403000000009833
urtwn0 at uhub1 port 4 "Realtek 802.11n WLAN Adapter" rev 2.00/2.00 addr 3
urtwn0: MAC/BB RTL8188CUS, RF 6052 1T1R, address 80:1f:02:ab:b4:3f
uhidev0 at uhub3 port 3 configuration 1 interface 0 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev0: iclass 3/1, 9 report ids
ukbd0 at uhidev0 reportid 1: 8 variable keys, 6 key codes, country code 13
wskbd0 at ukbd0: console keyboard, using wsdisplay0
uhid0 at uhidev0 reportid 9: input=0, output=0, feature=3
uhidev1 at uhub3 port 3 configuration 1 interface 1 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
uhidev1: iclass 3/0, 68 report ids
uhid1 at uhidev1 reportid 68: input=511, output=0, feature=0
ubcmtp0 at uhub3 port 3 configuration 1 interface 2 "Apple Inc. Apple Internal Keyboard / Trackpad" rev 2.00/0.90 addr 2
wsmouse0 at ubcmtp0 mux 0
uhidev2 at uhub3 port 5 configuration 1 interface 0 "Apple Computer, Inc. IR Receiver" rev 2.00/0.16 addr 3
uhidev2: iclass 3/0, 38 report ids
uhid2 at uhidev2 reportid 36: input=4, output=0, feature=0
uhid3 at uhidev2 reportid 37: input=4, output=0, feature=0
uhid4 at uhidev2 reportid 38: input=4, output=0, feature=0
uhub4 at uhub3 port 6 "Apple Inc. BRCM2046 Hub" rev 2.00/1.00 addr 4
ugen1 at uhub4 port 1 "Apple Inc. Bluetooth USB Host Controller" rev 2.00/2.07 addr 5
uhidev3 at uhub4 port 2 configuration 1 interface 0 "Apple Computer product 0x820a" rev 2.00/1.00 addr 6
uhidev3: iclass 3/1, 1 report id
ukbd1 at uhidev3 reportid 1: 8 variable keys, 6 key codes
wskbd1 at ukbd1 mux 1
wskbd1: connecting to wsdisplay0
uhidev4 at uhub4 port 3 configuration 1 interface 0 "Apple Computer product 0x820b" rev 2.00/1.00 addr 7
uhidev4: iclass 3/1, 2 report ids
ums0 at uhidev4 reportid 2: 3 buttons
wsmouse1 at ums0 mux 0
vscsi0 at root
scsibus3 at vscsi0: 256 targets
softraid0 at root
scsibus4 at softraid0: 256 targets
root on wd0a (3c22844f2527623e.a) swap on wd0b dump on wd0b
`
