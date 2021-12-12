### CONFIG NODE

#### FOOSHA
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
hwaddress ether 52:0d:61:93:5d:57

auto eth1
iface eth1 inet static
	address 192.183.7.145
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 192.183.7.149
	netmask 255.255.255.252
```

#### WATER7
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
	address 192.183.7.146
	netmask 255.255.255.252
  	gateway 192.183.7.145
  
auto eth1
iface eth1 inet static
	address 192.183.7.1
	netmask 255.255.255.128
  
auto eth2
iface eth2 inet static
  address 192.183.7.129
  netmask 255.255.255.248


auto eth3
iface eth3 inet static
  address 192.183.0.1
  netmask 255.255.252.0
```

#### GUANHAO
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
	address 192.183.7.150
	netmask 255.255.255.252
  	gateway 192.183.7.149
  
auto eth1
iface eth1 inet static
	address 192.183.6.1
	netmask 255.255.255.0
  
auto eth2
iface eth2 inet static
  address 192.183.7.137
  netmask 255.255.255.248


auto eth3
iface eth3 inet static
  address 192.183.4.1
  netmask 255.255.254.0
```

#### Blueno
```
auto eth0
iface eth0 inet static
#auto eth0
#iface eth0 inet dhcp
	address 192.183.7.2
	netmask 255.255.255.128
	gateway 192.183.7.1
```

#### Doriki
```
auto eth0
iface eth0 inet static
	address 192.183.7.130
	netmask 255.255.255.248
	gateway 192.183.7.129	
```

#### Jipangu
```
auto eth0
iface eth0 inet static
	address 192.183.7.131
	netmask 255.255.255.248
	gateway 192.183.7.129
```

#### Cipher
```
auto eth0
iface eth0 inet static
#auto eth0
#iface eth0 inet dhcp
	address 192.183.0.2
	netmask 255.255.252.0
	gateway 192.183.0.1
```

#### Fukurou
```
auto eth0
iface eth0 inet static
#auto eth0
#iface eth0 inet dhcp
	address 192.183.4.2
	netmask 255.255.254.0
	gateway 192.183.4.1
```

#### Maingate
```
auto eth0
iface eth0 inet static
	address 192.183.7.139
	netmask 255.255.255.248
	gateway 192.183.7.137
```

#### Jorge
```
auto eth0
iface eth0 inet static
	address 192.183.7.138
	netmask 255.255.255.248
	gateway 192.183.7.137
```

#### Elena
```
auto eth0
iface eth0 inet static
#auto eth0
#iface eth0 inet dhcp
	address 192.183.6.2
	netmask 255.255.255.0
	gateway 192.183.6.2
```


### Routing

#### FOOSHA
```
# !/bin/sh

route add -net 192.183.7.0 netmask 255.255.255.128 gw 192.183.7.146
route add -net 192.183.7.128 netmask 255.255.255.248 gw 192.183.7.146
route add -net 192.183.0.0 netmask 255.255.252.0 gw 192.183.7.146

route add -net 192.183.6.0 netmask 255.255.255.0 gw 192.183.7.150
route add -net 192.183.7.136 netmask 255.255.255.248 gw 192.183.7.150
route add -net 192.183.4.0 netmask 255.255.254.0 gw 192.183.7.150
```

#### nano ./.bashrc
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.183.0.0/16
/root/config.sh
echo "nameserver 192.183.122.1" > /etc/resolv.conf
```

berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis
menggunakan bantuan DHCP server.
Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

### Doriki (DNS Server)

##### nano config.sh
```
# !/bin/sh

apt-get update
apt-get install bind9 -y
```

### Jipangu (DHCP Server)

##### nano config.sh
```
# !/bin/sh

apt-get update
apt-get install isc-dhcp-server -y
```

##### nano isc-dhcp-server
```
INTERFACES="eth0"
```

#### nano dhcpd.conf
```
# Blueno
subnet 192.183.7.0 netmask 255.255.255.128 {
    range 192.183.7.2 192.183.7.126;
    option routers 192.183.7.1;
    option broadcast-address 192.183.7.127;
    option domain-name-servers 192.183.7.130;
    default-lease-time 360;
    max-lease-time 7200;
}

# Cipher
subnet 192.183.0.0 netmask 255.255.252.0	 {
    range 192.183.0.2 192.183.3.254;
    option routers 192.183.0.1;
    option broadcast-address 192.183.3.255;
    option domain-name-servers 192.183.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}

# Fukurou
subnet 192.183.4.0 netmask 255.255.254.0 {
    range 192.183.4.2 192.183.5.254;
    option routers 192.183.4.1;
    option broadcast-address 192.183.5.255;
    option domain-name-servers 192.183.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}

# Elena
subnet 192.183.6.0 netmask 255.255.255.0 {
    range 192.183.6.2 192.183.6.254;
    option routers 192.183.6.1;
    option broadcast-address 192.183.6.255;
    option domain-name-servers 192.183.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}

# Routing dari Jipangu ke router Water7
subnet 192.183.7.128 netmask 255.255.255.248 {
        option routers 192.183.7.129;
}

# Routing dari Jipangu ke router Guanhao
subnet 192.183.7.136 netmask 255.255.255.248 {
        option routers 192.183.7.137;
}

# Routing dari Jipangu ke router Foosha
subnet 192.183.7.144 netmask 255.255.255.252 {
        option routers 192.183.7.145;
}

# Routing dari Foosha ke Guanhao
subnet 192.183.7.148 netmask 255.255.255.252 {
        option routers 192.183.7.149;
}

# Water7
#subnet 192.183.7.144 netmask 255.255.255.252 {
#        option routers 192.183.7.145;
#}

# Guanhao
#subnet 192.183.7.148 netmask 255.255.255.252 {
#        option routers 192.183.7.149;
#}

host FOOSHA {
    hardware ethernet 52:0d:61:93:5d:57;
    fixed-address 192.168.122.66;
}
```

### Water7 (DHCP Relay)

##### nano config.sh
```
# !/bin/sh

apt-get update
apt-get install isc-dhcp-relay -y

service isc-dhcp-relay start
```

##### nano /etc/default/isc-dhcp-relay
```
# What servers should the DHCP relay forward requests to?
SERVERS="192.183.7.131"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

### Guanhao (DHCP Relay)

##### nano config.sh
```
# !/bin/sh

apt-get update
apt-get install isc-dhcp-relay -y

service isc-dhcp-relay start
```

##### nano /etc/default/isc-dhcp-relay
```
# What servers should the DHCP relay forward requests to?
SERVERS="192.183.7.131"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
```

### Maingate (Web Server)

##### nano config.sh
```
# !/bin/sh

apt-get update
apt-get install isc-dhcp-relay -y

service isc-dhcp-relay start
```

### Jorge (Web Server)

##### nano config.sh
```
# !/bin/sh

apt-get update
```

### Blueno (Client)

#### All

Config ditambah:
```
auto eth0
iface eth0 inet dhcp
```

##### nano config.sh
```
# !/bin/sh

```

### Cipher
```
# !/bin/sh

```

### Fukurou
```
# !/bin/sh

```

### Elena
```
# !/bin/sh

```

## 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

nano config.sh on Foosha
```
!!!iptables -t nat -A POSTROUTING -s 192.183.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.98
```

Yang Butuh Update (Water7, Doriki, Jipangu, dan Guanhao)
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

Test dengan ping



## 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang memiliki ip DHCP dan DNS Server demi menjaga keamanan.

nano config.sh on Foosha
```
iptables -A FORWARD -p tcp --dport 80 -d 192.183.7.128/29 -i eth0 -j DROP
```

```
# doriki DNS
iptables -A INPUT -s 192.183.7.130 -j DROP
# subnet a2, a3, a6, a7
iptables -A INPUT -s 192.183.7.0/25 -j DROP
iptables -A INPUT -s 192.183.0.0/22 -j DROP
iptables -A INPUT -s 192.183.4.0/23 -j DROP
iptables -A INPUT -s 192.183.6.0/24 -j DROP
```

```
iptables -A INPUT -p tcp --dport 80 -j DROP
```

Test
```
nmap 192.183.7.128
nmap 192.183.7.131
```

