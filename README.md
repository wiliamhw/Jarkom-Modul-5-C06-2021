# Jarkom-Modul5-C06-2021

## Anggota Kelompok : 
- 05111940000004 | Nizar Mayraldo
- 05111940000087 | William Handi Wijaya
- 05111940000202 | Muhammad Naufaldillah

## A. Topologi Jaringan
![Topologi Jaringan](https://user-images.githubusercontent.com/52129348/144748505-68a8193c-c485-4b07-9787-bae9b98ce504.png)

## B. Subnetting (CIDR)
Berikut ini adalah pengaturan IP pada file `/etc/network/interfaces` untuk masing-masing nodes pada UML:

### Foosha (Router)
```
# DHCP config for eth0
auto eth0
iface eth0 inet dhcp

# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.17.1.1
	netmask 255.255.255.252
  
# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.17.5.1
	netmask 255.255.255.252
```

### Water7 (Router)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.1.2
	netmask 255.255.255.252
  
# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.17.0.1
	netmask 255.255.255.128
    
# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.17.1.9
	netmask 255.255.255.248
    
# Static config for eth3
auto eth3
iface eth3 inet static
	address 10.17.0.129
	netmask 255.255.255.128
```

### Guanhao (Router)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.5.2
	netmask 255.255.255.252
    
# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.17.4.1
	netmask 255.255.255.128
    
# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.17.5.9
	netmask 255.255.255.248
    
# Static config for eth3
auto eth3
iface eth3 inet static
	address 10.17.4.129
	netmask 255.255.255.128
```

### Jipangu (DHCP Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.1.11
	netmask 255.255.255.252
	gateway 10.17.1.9
```

### Doriki (DNS Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.1.10
	netmask 255.255.255.252
	gateway 10.17.1.9
```

### Maingate (Web Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.5.11
	netmask 255.255.255.252
	gateway 10.17.5.9
```

### Jorge (Web Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.5.10
	netmask 255.255.255.252
	gateway 10.17.5.9
```

### Blueno (Client dengan 100 host)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.0.2
	netmask 255.255.255.128
	gateway 10.17.0.1
```

### Cipher (Client dengan 100 host)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.0.130
	netmask 255.255.255.128
	gateway 10.17.0.129
```

### Elena (Client dengan 300 host)
```
auto eth0
iface eth0 inet static
	address 10.17.4.2
	netmask 255.255.255.128
	gateway 10.17.4.1
```

### Fukurou (Client dengan 200 host)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.4.130
	netmask 255.255.255.128
	gateway 10.17.4.129
```

## C. Routing (CIDR)
Berdasarkan topologi pada soal A, karena menggunakan teknik subneting CIDR, maka routing yang diperlukan lebih sederhana. Routing hanya perlu dilakukan di node Foosha seperti berikut:
```
route add -net 10.17.0.0 netmask 255.255.254.0 gw 10.17.1.2
route add -net 10.17.4.0 netmask 255.255.252.0 gw 10.17.5.
```

## D. DHCP Server-Relay
-
