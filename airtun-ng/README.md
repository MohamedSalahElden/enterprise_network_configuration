# airtun-ng

```bash
┌──(kali㉿kali)-[~/Desktop/aircrack-ng/scripts/airgraph-ng]
└─$ airtun-ng --help                                                         1 ⨯

  Airtun-ng 1.6  - (C) 2006-2020 Thomas d'Otreppe
  Original work: Martin Beck
  https://www.aircrack-ng.org

  usage: airtun-ng <options> <replay interface>

      -x nbpps         : number of packets per second (default: 100)
      -a bssid         : set Access Point MAC address
                         In WDS Mode this sets the Receiver
      -i iface         : capture packets from this interface
      -y file          : read PRGA from this file
      -w wepkey        : use this WEP-KEY to encrypt packets
      -p pass          : use this WPA passphrase to decrypt packets
                         (use with -a and -e)
      -e essid         : target network SSID (use with -p)
      -t tods          : send frames to AP (1) or to client (0)
                         or tunnel them into a WDS/Bridge (2)
      -r file          : read frames out of pcap file
      -h MAC           : source MAC address

  WDS/Bridge Mode options:
      -s transmitter   : set Transmitter MAC address for WDS Mode
      -b               : bidirectional mode. This enables communication
                         in Transmitter's AND Receiver's networks.
                         Works only if you can see both stations.

  Repeater options:
      --repeat         : activates repeat mode
      --bssid <mac>    : BSSID to repeat
      --netmask <mask> : netmask for BSSID filter

      --help           : Displays this usage screen

```


```bash
airtun-ng -w 73616c6168<wep key in hex> -a <mac address of the AP> wlan0mon
```