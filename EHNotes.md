# Ethical Hacking Notes

## SETUP:
### Note: [Get Kali Ready PDF](https://udemy-assets-on-demand2.udemy.com/2016-05-09_15-45-55-4349742c6c154253acb9919626d7395a/f19f5734-e028-4d0a-8027-6faf0b724695.pdf?nva=20190131230522&filename=GetKaliReadyCourserev-5-8-16.pdf&download=True&token=0d246adc3c197158cf34d)
### 1. [Install Virtualbox](https://www.virtualbox.org/)
### 2. [Download Kali .iso](https://www.kali.org/downloads/)
### 3. Install Kali in VB
### 4. Change Network Manager Settings
* ``` cd /etc/NetworkManager ```
* ``` nano NetworkManager ```
* change "managed" to "true"
### 5. Change Kali Repo Sources
* ``` cd /etc/apt ```
* ``` nano sources.list ```
* cut out everything and paste in these two lines or the [current repos](https://docs.kali.org/general-use/kali-linux-sources-list-repositories):
  * deb http://http.kali.org/kali kali-rolling main non-free contrib
  * deb-src http://http.kali.org/kali kali-rolling main non-free contrib
* ``` apt-get update && apt-get update -y ```
### 6. Setup VB Guest Additions
* ``` apt-cache search header | grep -i linux ```
* search for the one ending in "–kali-all-amd64"
* copy that line
* ``` apt-get install linux-headers-[version numbers]-kali1-all-amd64 ```
* ``` apt-get update && apt-get update -y ```
* ``` reboot ```
* VMBox > Devices > Insert Guest Additions CD Image > Run > OK
* ``` cd /media/cdrom0 ```
* ```cp VBoxLinuxAdditions.run /root/Desktop ```
* ``` cd Desktop ```
* ``` ./VBoxLinuxAdditions.run ```
### 7. Tor OR ProxyChains
#### Setup Tor
* ``` apt-get install tor -y ```
* create a non-root user for the machine:
  * ``` adduser [whatever name you want] ```
* log in as non-root user
* open browser
* search for tor browser or go [here](https://www.torproject.org)
* extract to /Desktop
* ``` cd /Desktop/tor-browser_en-US ```
* ``` ./start-tor-browser.desktop ```
#### Run Tor
Changes IP address with random encrypted proxychains
* ``` cd /Desktop/tor-browser_en-US ```
* ``` ./start-tor-browser.desktop ```
* search for hidden wiki
* To check status, turn off/on:
  * ``` service tor [status || start || stop] ```
* To check for [DNS leak](https://www.dnsleaktest.com)
#### Setup ProxyChains
Changes IP address with custom proxychains
* ``` nano /etc/proxychains.conf ```
* choose dynamic_chain
* add some extra proxy servers to the end of the file, after "[ProxyList]":
  * google "free socks5 proxies" OR [socks proxy](www.socks-proxy.net)
* ``` proxychains [name of browser] [website] ```
#### Run ProxyChains
* ``` proxychains [name of browser] [website] ```
### 8. Change DNS (Preamble to setup VPN)
Change DNS from the default one provided by ISP to a public one, extra anonymity
* check dns server run:
  * ``` cat /etc/resolv.conf ```
* Change DNS:
  * google: "opendns ip address" OR use these 208.67.222.222, 208.67.220.220, 8.8.8.8
  * ``` nano /etc/dhcp/dhclient.conf ```
  * uncomment "prepend domain-name-servers 127.0.0.1;"
  * edit "predend" line by replacing the 127... address with the two opendns address separated by a comma
  * Restart Network Manager to take effect:
    * ``` service network-manager restart ```
* check dns server run (compare to first step):
  * ``` cat /etc/resolv.conf ```
### 9. VPN
#### Setup VPN
Bypass firewall settings
* open browser > about:config > media.peerconnection.enabled > false
* search for "open vpn free" OR  go to [vpnbook](www.vpnbook.com)
* download VPN
* take note of username & password associated with localized VPN
* close browser
* ``` cd Downloads ```
* ``` unzip VPNBook.com-OpenVPN-[country-code].zip ```
* List VPN protocol files:
  * ``` ls Downloads ```
* ``` openvpn vpnbook-[country-code]-[protocol].ovpn ```
* Enter user name and pass from OpenVPN site
#### Run VPN
* ``` openvpn vpnbook-[country-code]-[protocol].ovpn ```
* Enter user name and pass from OpenVPN site
* check for [DNS leak](https://www.dnsleaktest.com)
### 10. MacChanger
Change the address of the hardware assoicated with your machine (visible only in the local network)
Note: doesn't work on virtual machines, if one wants to change the MAC address of a VM go into settings of the VB.
* Check current addresses and interface:
  * ``` ifconfig ```
* Check current addresses associated to an interface:
  * ``` macchanger --show eth0 ```
* Set a random MAC:
  * ``` macchanger -r eth0 ```
#### Change MAC Address on startup using CronTab
##### Crontab
Assign tasks to run on a schedule or following an event
* create an executable file/script:
  * ``` nano [aFileName].sh ```
  * ``` #!/bin/bash ```
  * ``` ifconfig eth0 down ```
  * ``` sleep 2 ```
  * ``` macchanger -r eth0 ```
  * ``` sleep 2 ```
  * ``` ifconfig eth0 up ```
* ensure the file is executable:
  * ``` chmod +x [aFileName].sh ```
* open CronTab:
  * ``` crontab -e ```
* At the bottom add:
  * ``` @reboot [path/to/aFileName].sh ```

## Tools
### 1. Nmap (Network Mapper)
Scan IP addresses for metadata
Note: Free to use site for scanning - [scanme.nmap.org](scanme.nmap.org)
* Find IP addresses associated with domain name:
  * ``` nslookup [url] ```
* Find port information associated with domain name:
  * ```nmap [url] ```
* Scan a range of IPs (e.g. 0 - 255) that match a certain sequence (e.g. -oG [an.ipa.ddr.ess]), on a single port (e.g. 22) & send the report to a file (e.g. > /home/scan):
  * ``` nmap -oG [192.168.1.0-255] -p 22 -vv > /home/scan ```
  * Filter results from above matching "Up":
    * ``` cat scan | grep Up ```
  * Filter results from above matching "Up", breaking at " " & send just the IPs (i.e. '{print $2}' (i.e. the 2nd item to follow a " ")) to a new file:
    * ``` cat scan | grep Up | awk -F " " '{print $2}' > scan2 ```
  * Full scan the now filtered list of up and open hosts & send to a new file:
    * ``` nmap -iL scan2 -vv > scan3 ```
  * Pass scripts to Nmap:
    * [Nmap Scripts](https://nmap.org/nsedoc/)
### 2. Who Is ...
Collect general owner data of an IP
* Google:
  * "Who is [an.ipa.ddr.ess]"
* Terminal (1000 scans / day):
  * ``` curl ipinfo.io/[an.ipa.ddr.ess] ```
* IP Info site:
  * [ipinfo.io](https://ipinfo.io)
### 3. Exploit-DB
Search for vulnerabilities of hardware
* [Exploit-DB](www.exploit-db.com)
### 4. Aircrack-Ng
IT DOES A THING
* Install:
  * ``` apt-get install aircrack-ng ```
### 5. Reaver
IT DOES A THING
* Download:
  * [Reaver](https://code.google.com/archive/p/reaver-wps/downloads)
* Extract:
  * ``` tar xvzf reaver[ver.sion.num].tgz -C /path/to/desired/location ```
### 6. Crunch
Random string/password generator
* Download:
  * [Crunch](https://sourceforge.net/projects/crunch-wordlist/)
* Extract:
  * ``` tar xvzf crunch[ver.sion.num].tgz -C /path/to/desired/location ```

## Cracking wifi WPA/WPA2
Tools to be used:
* Aircrack-Ng
* Reaver
* Crunch

### Cracking a router
* Find your wifi interface:
  * ``` ifconfig ```
* Shut down interface:
  * ``` ifconfig [wifi interface] down ```
* Switch interface to monitor mode:
  * ``` iwconfig [wifi interface] mode monitor ```
* Turn on interface:
  * ``` ifconfig [wifi interface] up ```
