# enterprise network configuration

## steps
1. setup AP (hostapd)
2. configure DHCP server (udhclient) 

# 1. open authentication
## hostapd configuration
```bat
interface=wlan0
driver=nl80211
ssid=test
channel=1
```

## run hostapd with the configuration

```
└─$ sudo hostapd ./hostapd.conf     
```

## dhcp configuration

```
/etc/udhclient.conf
```

```bash
# Sample udhcpd configuration file (/etc/udhcpd.conf)

# The start and end of the IP lease block

start		192.168.0.20	#default: 192.168.0.20
end		192.168.0.254	#default: 192.168.0.254


# The interface that udhcpd will use

interface	wlan0		#default: eth0
```

## change the ip of wireless interface
```bash
└─$ sudo ifconfig wlan0 up 192.168.0.1  
```

## run udhclient dhcp server
```bash
└─$ sudo udhcpd -fS ./udhcpd.conf    
```
---
#### when client connects
##### DHCP
``` 
udhcpd: sending OFFER of 192.168.0.205
udhcpd: sending OFFER of 192.168.0.205
udhcpd: sending OFFER of 192.168.0.205
udhcpd: sending OFFER of 192.168.0.205
udhcpd: sending ACK to 192.168.0.205
```
#### AP
```
wlan0: interface state UNINITIALIZED->ENABLED
wlan0: AP-ENABLED 
wlan0: STA f6:c8:07:6f:21:c9 IEEE 802.11: authenticated
wlan0: STA f6:c8:07:6f:21:c9 IEEE 802.11: associated (aid 1)
wlan0: AP-STA-CONNECTED f6:c8:07:6f:21:c9
wlan0: STA f6:c8:07:6f:21:c9 RADIUS: starting accounting session 9DE23FFD3734024B
```
to test connectivity (apatche2ctl)
```
└─$ sudo apache2ctl start
```
then open the browser and type wlan0 ip address 192.168.0.1:80
---

# 2. WEP authentication
```bash
interface=wlan0
hw_mode=g
driver=nl80211
ssid=test
channel=6

auth_algs=1 #open authentication
wep_default_key=0
wep_key0="salah"
```
- auth_algs
    - bit 0 : open
    - bit 1 : shared key
- wep_default (the key id that the user will use by defaul 0 means that we will use wep_key0)

### logs
```
-------------------AP---------------------
wlan0: interface state UNINITIALIZED->ENABLED
wlan0: AP-ENABLED 
wlan0: STA 82:47:2f:d8:1c:81 IEEE 802.11: authenticated
wlan0: STA 82:47:2f:d8:1c:81 IEEE 802.11: associated (aid 1)
wlan0: AP-STA-CONNECTED 82:47:2f:d8:1c:81
wlan0: STA 82:47:2f:d8:1c:81 RADIUS: starting accounting session 96759F040F4868DF
------------------DHCP--------------------
udhcpd: sending OFFER of 192.168.0.156
udhcpd: sending OFFER of 192.168.0.156
udhcpd: sending OFFER of 192.168.0.156
udhcpd: sending ACK to 192.168.0.156
udhcpd: sending OFFER of 192.168.0.156
udhcpd: sending ACK to 192.168.0.156
```

# 3. WPA-PSK
```bash
interface=wlan0
hw_mode=g
driver=nl80211
ssid=test
channel=6

auth_algs=1 #open authentication
wpa=1 
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
wpa_passphrase=123456789
```
- wpa field
    - bit 0 WPA
    - bit 1 WPA2 
- wpa key managment
    - the algorithm that will manage the key exchange WPA-PSK (for WPA)
- wpa_pairwise
    - the cipher algorithm -> TKIP (for WPA)

logs
```bash
------------------AP----------------------
wlan0: AP-ENABLED 
wlan0: STA 46:21:76:3d:8b:4c IEEE 802.11: authenticated
wlan0: STA 46:21:76:3d:8b:4c IEEE 802.11: associated (aid 1)
wlan0: AP-STA-CONNECTED 46:21:76:3d:8b:4c
wlan0: STA 46:21:76:3d:8b:4c RADIUS: starting accounting session F4459D29AA450543
wlan0: STA 46:21:76:3d:8b:4c WPA: pairwise key handshake completed (WPA)
wlan0: STA 46:21:76:3d:8b:4c WPA: group key handshake completed (WPA)
-----------------dhcp----------------------
udhcpd: sending OFFER of 192.168.0.90
udhcpd: sending OFFER of 192.168.0.90
udhcpd: sending OFFER of 192.168.0.90
udhcpd: sending ACK to 192.168.0.90
```
# 4. WPA2-PSK
```bash
interface=wlan0
hw_mode=g
driver=nl80211
ssid=test
channel=6

auth_algs=1 #open authentication
wpa=2
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP
wpa_passphrase=123456789
```
# multiple AP on hostapd
1. both AP have to be on the same channel

- common section between APs
    ```
    # common section 
    interface=wlan0
    hw_mode=g
    driver=nl80211
    channel=6
    ```
- AP 1
    ```
    # first AP configuration
    ssid=open_demo
    auth_algs=1
    ```

- AP 2
    ```
    # second AP configuration
    bss=wlan10
    ssid=wpa-psk-demo
    auth_algs=1 #open authentication
    wpa=1
    wpa_key_mgmt=WPA-PSK
    wpa_pairwise=TKIP
    wpa_passphrase=123456789
    ```

- in the hostapd.conf file `/etc/hostapd.conf` in the section `#### multiple BSSID support ####`