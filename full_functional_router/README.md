# fully functional AP

[source blog](https://hakin9.org/create-a-fake-access-point-by-anastasis-vasileiadis/)


## 1. update linux
``` bash
┌──(kali㉿kali)-[~]
└─$ sudo apt-get update                        
```
## 2. install hostapd , Dnsmasq
- dnsmasq is a DNS , DHCP server

``` bash
┌──(kali㉿kali)-[~]
└─$ sudo apt install hostapd dnsmasq     
```
## 3. check for wireless cards and put it in monitoring mode
```bash
┌──(kali㉿kali)-[~]
└─$ iwconfig 
lo        no wireless extensions.

eth0      no wireless extensions.

wlan0     IEEE 802.11  ESSID:off/any  
          Mode:Managed  Access Point: Not-Associated   Tx-Power=15 dBm   
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Power Management:off
          
wlan1mon  IEEE 802.11  Mode:Monitor  Frequency:2.447 GHz  Tx-Power=20 dBm   
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off                     
```

```bash
                                                                                                             
┌──(kali㉿kali)-[~]
└─$ sudo ifconfig wlan0 down                                                       sudo iwconfig wlan0 mode monitor                                                    
sudo ifconfig wlan0 up                                                         
```

## 4. create configuration file for hostapd

```bash 
interface=wlan0
driver=nl80211
ssid=SALAH_AP
hw_mode=g
channel=11
ignore_broadcast_ssid=0

auth_algs=1
wpa=2
wpa_passphrase=+201018220471
wpa_key_mgmt=WPA-PSK
wpa_pairwise=CCMP
wpa_group_rekey=86400
ieee80211n=1
wme_enable=1
```
## 5. start the AP
```
└─$ sudo hostapd ./hostapd.conf                                             
```
## 6. configure DHCP , DNS (dnsmasq)
```
interface=wlan0
dhcp-range=192.168.1.2,192.168.1.30,255.255.255.0,12h
dhcp-option=3,192.168.1.1
dhcp-option=6,192.168.1.1
server=8.8.8.8
log-queries
log-dhcp
listen-address=127.0.0.1
```
## 7. assign network interface address 
- to ip outside the dhcp range
```
└─$ sudo ifconfig wlan0 up 192.168.1.1 netmask 255.255.255.0
```
## 8. add default route for all the DHCP clients address range
```bash 
┌──(kali㉿kali)-[~/Desktop/enterprise_network_configuration/full_functional_router]
└─$ sudo route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.1
SIOCADDRT: Network is unreachable
                      
```

- check if the network exist
```bash
└─$ ping 192.168.1.1   
```
- if the network dont exist (that is may be) you add ip address to the interface then you run hostapd (which over write the previous configuration including the added ip address)
- run hostapd then set the ip
- then add the default route for all the network clients
```bash
└─$ sudo route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.1
```
## run dnsmasq
```
└─$ sudo dnsmasq -C dnsmasq.conf -d                                                                     
```
## 8. give internet to clients
```
└─$ sudo iptables --table nat --append POSTROUTING --out-interface eth0 -j MASQUERADE                  
└─$ sudo iptables --append FORWARD --in-interface wlan0 -j ACCEPT
```
## 9. enable ip forwarding
```
└─$ sudo sysctl net.ipv4.ip_forward=1                                     
```