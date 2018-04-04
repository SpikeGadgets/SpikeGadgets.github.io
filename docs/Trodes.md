---
layout: page
title: Installing Trodes
---
# Table of Contents

- [Windows](#windows)
- [Linux (Ubuntu)](#linux-ubuntu)
- [MacOS](#macos)
- [Further Computer Setup](#further-computer-setup)
    - [USB](#usb)
        - [Ubuntu USB setup](#ubuntu-usb-setup)
    - [Ethernet (optional)](#ethernet-optional)
        - [Ubuntu Ethernet setup](#ubuntu-ethernet-setup)
        - [Windows Ethernet setup](#windows-ethernet-setup)
    - [Enabling Core Dumps on Linux](#enabling-core-dumps-on-linux)

## Windows

1. Install the [FTDI D2XX Driver](http://www.ftdichip.com/Drivers/D2XX.htm). 
    - For easy installation, use the "setup executable" listed in the comments for Windows. 

2. Download the latest release of the Trodes software suite here: https://bitbucket.org/mkarlsso/trodes/downloads/

3. Uncompress the downloaded folder and copy to a desired location on your machine.

4. Double-click on Trodes.exe to run. 
 
## Linux (Ubuntu)

1. Install the [FTDI D2XX Driver](http://www.ftdichip.com/Drivers/D2XX.htm). 

    - Follow the instructions in the first section of their [README](http://www.ftdichip.com/Drivers/D2XX/Linux/ReadMe-linux.txt)
    - Also follow the Ubuntu USB setup below

2. Download the latest release of the Trodes software suite here: https://bitbucket.org/mkarlsso/trodes/downloads/

3. Uncompress the downloaded folder and copy to a desired location on your machine.

4. Download required multimedia plugins for the camera module:

    ```bash
    sudo apt-get install gstreamer0.10-plugins-base 
    sudo apt-get install gstreamer0.10-plugins-good
    ```

5. Make sure Trodes program has correct permissions. Right-click on Trodes and select 'Properties' and navigate to the 'Permissions' tab.  Make sure that 'Allow executing file as program' is checked.

6. Double-click on Trodes to run. 

## MacOS

1. Install the [FTDI D2XX Driver](http://www.ftdichip.com/Drivers/D2XX.htm). 

    - Run the D2xxHelper program listed in the comments section to prevent OS X 10.11 (El Capitan) claiming the device as a serial port.

2. Download the latest release of the Trodes software suite here: [https://bitbucket.org/mkarlsso/trodes/downloads/](https://bitbucket.org/mkarlsso/trodes/downloads/)

3. Uncompress the downloaded folder and copy to a desired location on your machine.

4. (For versions before 1.6.1) Double click the "install.command" file.  A Terminal window will open and run the install script, close it when it is finished.

5. Double-click on Trodes to run. A warning message that the program was downloaded from the internet will appear the first time you run the program.  Also, you may need to change your security settings ("Allow apps downloaded from") to "Mac App Store and identified developers" for versions 1.6.1 and later. Before 1.6.1 this needs to be set to "Anywhere". 

---

## Further Computer Setup

Either USB or Ethernet source can be used, and require different setup steps depending on the OS used.

### USB

To use USB connection, you must install the FTD2XX driver. Follow directions in the ‘comments’ section for your OS here:

[http://www.ftdichip.com/Drivers/D2XX.htm](http://www.ftdichip.com/Drivers/D2XX.htm)

On Windows USB connectivity works without any extra setup. For MacOS, make sure you run the D2xxHelper program to prevent OS X 10.11 (El Capitan) claiming the device as a serial port.

#### Ubuntu USB setup

On Ubuntu Linux, to connect to the MCU and ECU via USB, you must tell your OS how to handle SpikeGadgets hardware when it is plugged in. You only need to do this once. Run the following command (all on one line) to copy the udev rules file to your machine:

```bash
sudo wget https://bitbucket.org/mkarlsso/trodes/downloads/spikegadgets.rules -O /etc/udev/rules.d/spikegadgets.rules
```

Rules will be applied the next time you connect the hardware. If already connected, unplug and replug the USB cable. The .rules file is also distributed with the precompiled binary, in Resources/SetupHelp, and in the source repository, at Resources/Linux, so you can copy from there instead of using ‘wget’ if you want.

### Ethernet (optional)

If you plan to use ethernet (UDP) to connect to the MCU, you will need a dedicated ethernet port on the computer for the MCU connection. Configure this port for IPv4 connection with the following address and subnet:

```text
Address: 192.168.0.2
Subnet: 255.255.255.0 
```

You also need to set the maximum transmission unit (MTU) for the connection to 9000 or more. Make sure there is no firewall active for this port (it may try to prevent a connection to the MCU). Next, adjust the size of the write/receive buffers used by sockets. 

#### Ubuntu Ethernet setup

For Ubuntu Linux, edit /etc/sysctl.conf (as root) and add the following lines:

``` text
net.core.rmem_max=8388607
net.core.rmem_default=8388607
net.core.wmem_max=8388607
net.core.wmem_default=8388607
```

#### Windows Ethernet setup

For Windows, adjusting the size of receive buffers is done using a different procedure. To set the default size, edit (or create) the following DWORD registry keys (using ‘regedit’ program from the start menu run box) and restart Windows: 

```text
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Afd\Parameters]
DefaultReceiveWindow (REG_DWORD) = 8384512 (decimal)
DefaultSendWindow (REG_DWORD) = 8384512 (decimal)
```

### Enabling Core Dumps on Linux

On Ubuntu, if the program crashes, it usually generates a file to be used to debug if the settings are configured. This can't be done after the crash has already happened, so it is advised to enable it. In the rare event a crash occurs, Spike Gadgets developers can debug the issue. 

To enable: 

Edit the file /etc/security/limits.conf (as root), to include the following lines (NB: The line begins with an asterisk):

```text
# (Trodes) Enable core dumps
* soft core unlimited
```

Edit /etc/sysctl.conf (as root) and add the following lines:

```text
# (Trodes) Include process id (PID) in core dump file names
kernel.core_uses_pid=1
```

Log out and log back in for settings to take effect.