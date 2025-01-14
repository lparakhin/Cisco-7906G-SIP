# Cisco-7906G-SIP
Cisco 7906G SIP Configuration Files (for 3rd party PBXs)

## TFTP/DHCP Configuration

During boot the handsets will discover the TFTP server via DHCP option 66 and/or DHCP option 150. For this example I set option 66 of our VoIP VLAN to the local IP address of our TFTP server. All of the configuration files, firmware files and other customisation files reside in the root directory of the TFTP server, in this case it was `\TFTPBOOT`. Download portable TFTP server here http://tftpd32.jounin.net/tftpd32_download.html, set IP address and option.

![image](https://user-images.githubusercontent.com/78740726/206427679-10181ef1-ced4-49dd-b59c-5a42435e2cee.png)


## Base Configuration Files

Each handset will require an SEP configuration file. The filename must be `SEP000000000000.cnf.xml` where the `000000000000` is the MAC address of the Cisco handset.

In the example SEP configuration file, the following values have been set:

    sip.provider.com  <!-- This is the FQDN of the PBX, it can also be an IP address -->
    123.123.123.123   <!-- This is the public IP address of the network the handset resides on, this if for NAT and may not be needed -->
    222               <!-- This is the numerical extension number the handset will have, this must exist on the remote PBX first -->
    pbx-username      <!-- This is the register username for the extension -->
    Pa$$w0rd          <!-- This is the register password for the extension -->
    *55               <!-- This is the direct dial for the PBX's voicemail -->
    
All other settings can be ignored for the purpose of the inital configuration. You must change every occurance of the above settings throughout the configuration file.

## Firmware Versions and Upgrading

The firmware version that has been tested for these configuration files is `SIP11.9-4-2SR1-1S`. You can download the firmware files directly from Cisco - you will need to register for a free account. At the time of writing the link for the firmware files is [here](https://software.cisco.com/download/release.html?mdfid=280607214&softwareid=282074288&os=&release=9.4(2)SR3&relind=AVAILABLE&rellifecycle=&reltype=latest&i=!pp), however, I have included the firmware files in this repository for reference.

## Dialplan

The included `dialplan.xml` gives some examples available. The dialplan file tells the handset how long to pause before dialling a number once it has been entered.

Simple dialplan:
```xml
<DIALTEMPLATE>
<TEMPLATE MATCH="*" Timeout="3"/>
</DIALTEMPLATE>
```

## Ringtones

I have included a `ringlist.xml` file as an example of how to add new ringtones to the handsets. If you monitor the TFTP server logs when navigating the handset menu and requesting a new background or ringtone, you will see which files the handset requests. This is very useful when setting up the TFTP file structure.

When including ringtone .raw files, it's simplest to include them in the root directory of the TFTP server along with the firmware and configuration files.

## Reset

Power on the phone while holding the # key.

### Soft Reset

The code for the soft factory reset is: 123456789*0#
A soft reset does the following:
5. deletes key files on the phone including /etc/init.tab
6. reboots
7. phone recovers default loads file from TFTP server

### Total Hard Reset

The code for the total hard reset is: 3491672850*#
A total hard reset formats /flash0 blowing away everything except CNU. All settings and personalization of the phone is removed.

### Hard Reset with Network Setting Preservation

The code for the network preservation hard reset is: *7412369#085
And it also does a complete format of /flash0 — yet it preserves the network settings so that after the format, any static IP and other network settings are preserved. The settings are stored in volatile RAM, so it is important to not remove power to the phone during this process.



