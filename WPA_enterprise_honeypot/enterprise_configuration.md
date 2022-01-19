# enterprise configuration with hostapd
- interface configuration 
    ```bash
    interface=wlan0
    hw_mode=g
    driver=nl80211
    channel=6
    ```
- in the case of enterprise configuration
1. the key managment protocol should be
    `WPA-EAP` to use the EAP protocol
2. there is no passphrase in the EAP (as the radius server is the one who has passwords related with user names)


    ```bash
    ssid=wpa2ent
    auth_algs=1 #open authentication
    wpa=2
    wpa_key_mgmt=WPA-EAP
    rsn_pairwise=CCMP
    ```
3. set up the WPA enterprise parameters is should be in `/etc/hostapd.conf` in the `#### WPA/IEEE 802.1X-2004 related configuration #### ` section

    ```bash
    ieee8021x=1
    eapol_key_index_workaround=0
    ```
4. set the radis client config (for external radius server)
    ```bash
    own_ip_addr=127.0.0.1
    auth_server_addr=127.0.0.1
    auth_server_port=1812
    auth_server_shared_secret=testing123
    ```

5. run all
    ```bash
    └─$ sudo radiusd -X -s   
    ```

    ```bash
    └─$ sudo hostapd ./WPA_enterprise_honeypot/hostapd_WPA2_enterprise_PSK.conf

    ```


logs
```
----------------------AP------------------
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.11: authenticated
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.11: associated (aid 1)
wlan0: CTRL-EVENT-EAP-STARTED 66:f8:26:dd:c0:a9
wlan0: CTRL-EVENT-EAP-PROPOSED-METHOD vendor=0 method=1
wlan0: CTRL-EVENT-EAP-FAILURE2 66:f8:26:dd:c0:a9
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.1X: authentication failed - EAP type: 4 (MD5)
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.1X: Supplicant used different EAP type: 3 (unknown)
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.11: authenticated
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.11: associated (aid 1)
wlan0: CTRL-EVENT-EAP-STARTED 66:f8:26:dd:c0:a9
wlan0: CTRL-EVENT-EAP-PROPOSED-METHOD vendor=0 method=1
wlan0: CTRL-EVENT-EAP-FAILURE2 66:f8:26:dd:c0:a9
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.1X: authentication failed - EAP type: 4 (MD5)
wlan0: STA 66:f8:26:dd:c0:a9 IEEE 802.1X: Supplicant used different EAP type: 3 (unknown)


--------------------------radius--------------------------
Ready to process requests.
rad_recv: Access-Request packet from host 127.0.0.1 port 36128, id=0, length=211
        User-Name = "salah"
        NAS-IP-Address = 127.0.0.1
        Called-Station-Id = "3A-8F-68-73-BE-E3:wpa2ent"
        NAS-Port-Type = Wireless-802.11
        Service-Type = Framed-User
        NAS-Port = 1
        Calling-Station-Id = "66-F8-26-DD-C0-A9"
        Connect-Info = "CONNECT 54Mbps 802.11g"
        Acct-Session-Id = "9157376C526FC20D"
        Acct-Multi-Session-Id = "066C1C661203C075"
        X-Ascend-Home-Agent-UDP-Port = 1027076
        X-Ascend-Multilink-ID = 1027076
        X-Ascend-Num-In-Multilink = 1027073
        Framed-MTU = 1400
        EAP-Message = 0x0267000a0173616c6168
        Message-Authenticator = 0x48903ad9544ce8e7c3587170353561f7
# Executing section authorize from file /usr/local/etc/raddb/sites-enabled/default
+- entering group authorize {...}
++[preprocess] returns ok
++[chap] returns noop
++[mschap] returns noop
++[digest] returns noop
[suffix] No '@' in User-Name = "salah", looking up realm NULL
[suffix] No such realm "NULL"
++[suffix] returns noop
[eap] EAP packet type response id 103 length 10
[eap] No EAP Start, assuming it's an on-going EAP conversation
++[eap] returns updated
[files] users: Matched entry DEFAULT at line 205
++[files] returns ok
++[expiration] returns noop
++[logintime] returns noop
[pap] WARNING: Auth-Type already set.  Not setting to PAP
++[pap] returns noop
Found Auth-Type = EAP
# Executing group from file /usr/local/etc/raddb/sites-enabled/default
+- entering group authenticate {...}
[eap] EAP Identity
[eap] processing type md5
rlm_eap_md5: Issuing Challenge
++[eap] returns handled
Sending Access-Challenge of id 0 to 127.0.0.1 port 36128
        EAP-Message = 0x016800160410ac784ccc5567acd3b762d1c95b43bb59
        Message-Authenticator = 0x00000000000000000000000000000000
        State = 0xc8cb67e2c8a36344f7d6329dc5336b72
Finished request 0.
Ready to process requests.

```


6. to view the freeradius log file
