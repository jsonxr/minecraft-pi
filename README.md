# Minecraft Hotspot Server

This enables the creation of a minecraft server that can host it's own wifi hotspot. Take the raspberry pi to anyone's house, configure the outbound wifi, and connect to the `minecraft` wifi hotspot and have a LAN party.

https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md

# internet wifi

This is the wifi you use to get out to the internet.  In this example, the public wifi is `deathstar` and the password is `password`.  You can add additional networks and set their priorities. `priority` is not necessary it just determines the order it will connect.

```sh
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

```sh
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
  ssid="deathstar"
  priority=1
  psk="password"
}

network={
  ssid="backup"
  priority=2
  psk="password2"
}
```

# hotspot static ip

Configure your wireless card to have a static ip address. We will use the USB wifi card to do our external routing `wlan1` but you could just as easily use the built in wifi card `wlan0`.

The actual ip address/range can be any valid private ip address that doesn't conflict with your existing home router.  We are using `192.168.4.1` but it could be anything `192.168.x.x` or `10.x.x.x`.


`sudo nano /etc/dhcpcd.conf`


```sh
nohook lookup-hostname # <- Not sure if this is required

interface wlan1
  static ip_address=192.168.4.1/24
  nohook wpa_supplicant
```

# dhcp server

`sudo nano /etc/dnsmasq.conf`

```sh
interface=wlan1      # the hotspot wireless interface
dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h
```

# hostapd

`sudo nano /etc/hostapd/hostapd.conf`


```sh
interface=wlan1
ssid=minecraft
wpa_passphrase=3038292726
wpa=2
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
driver=nl80211
hw_mode=g
channel=7
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
rsn_pairwise=CCMP
```

# routing and masquerade

* Edit sysctl

  `sudo nano /etc/sysctl.conf`


  ```sh
  net.ipv4.ip_forward=1
  ```

* create iptables rules

    ```sh
    # MASQUERADE routing to wlan0
    sudo iptables -t nat -A  POSTROUTING -o wlan0 -j MASQUERADE
    sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
    ```

* Load rules at startup

  `sudo nano /etc/rc.local`

  ```sh
  # Place right above exit 0
  iptables-restore < /etc/iptables.ipv4.nat
  ```