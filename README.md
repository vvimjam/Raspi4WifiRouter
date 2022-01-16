# Raspi4WifiRouter

Before you being, it is recommended to install all updates & any drivers needed for your secondary WiFi adapter.

https://www.raspberrypi.com/documentation/computers/configuration.html
https://www.youtube.com/watch?v=laeOmNDE-Ac

```
sudo apt install hostapd
```
hostapd is used to setup Pi4 wifi as host instead of client.


```
sudo apt install dnsmasq
```
Dnsmasq is a lightweight, easy to configure, DNS forwarder and DHCP server. 

```
sudo DEBIAN_FRONTEND=noninteractive apt install -y netfilter-persistent iptables-persistent
```
Set netfilter to load netfilter rules at boot & persist ip tables.

```
sudo systemctl unmask hostapd.service

sudo systemctl enable hostapd.service
```

Setup dhcp
```
sudo nano /etc/dhcpcd.conf
```
Include at the end of dhcpcd.conf. (Ctrl + Shift + V to paste, Ctrl+X to exit, Y to save & enter to write)
```
interface wlan0
  static ip_address=10.20.1.1/24
  nohook wpa_supplicant
```

Forward ipv4 addresses
```
sudo nano /etc/sysctl.d/routed-ap.conf
```
Include in routed-ap.conf. (Ctrl + Shift + V to paste, Ctrl+X to exit, Y to save & enter to write)
```
net.ipv4.ip_forward=1
```

Setup iptable
```
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo netfilter-persistent save
```
Setup dhcp services
```
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.old
sudo touch /etc/dnsmasq.conf
sudo nano /etc/dnsmasq.conf
```

Include in dnsmasq.conf
300d=300 days until the ip is back on the iptable from any user.
```
interface=wlan0
dhcp-range=10.20.1.5,10.20.1.100,255.255.255.0,300d
domain=wlan
address=/rt.wlan/10.20.1.1
```

Setup access point
```
sudo touch /etc/hostapd/hostapd.conf
sudo nano /etc/hostapd/hostapd.conf
```

Include in hostapd.conf
**Absolutely set country code to your specific country. It is illegal to broadcast wifi in unintended freqs.
ssid=Magnus (Your network name)
hw_mode=a (Indicates 5Ghz)
```
country_code=US
interface=wlan0
ssid=YourNetworkName
hw_mode=a
channel=36
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=YourNetworkPassword
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

```
sudo reboot
```
