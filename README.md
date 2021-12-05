# Jarkom-Modul5-C06-2021

## Anggota Kelompok : 
- 05111940000004 | Nizar Mayraldo
- 05111940000087 | William Handi Wijaya
- 05111940000202 | Muhammad Naufaldillah

## A. Topologi Jaringan
![Topologi Jaringan](https://user-images.githubusercontent.com/52129348/144748505-68a8193c-c485-4b07-9787-bae9b98ce504.png)

## B. Subnetting (CIDR)
Berikut ini adalah pengaturan IP pada file `/etc/network/interfaces` untuk masing-masing nodes pada UML:

### Foosha (Router sekaligus DHCP Relay)
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

### Water7 (Router sekaligus DHCP Relay)
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

### Guanhao (Router sekaligus DHCP Relay)
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
	netmask 255.255.254.0
    
# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.17.5.9
	netmask 255.255.255.248
    
# Static config for eth3
auto eth3
iface eth3 inet static
	address 10.17.6.1
	netmask 255.255.255.0
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
	netmask 255.255.254.0
	gateway 10.17.4.1
```

### Fukurou (Client dengan 200 host)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.6.2
	netmask 255.255.255.0
	gateway 10.17.6.1
```

## C. Routing (CIDR)
Berdasarkan topologi pada soal A, karena menggunakan teknik subneting CIDR, maka routing yang diperlukan lebih sederhana. Routing hanya perlu dilakukan di node Foosha seperti berikut:
```
route add -net 10.17.0.0 netmask 255.255.254.0 gw 10.17.1.2
route add -net 10.17.4.0 netmask 255.255.252.0 gw 10.17.5.2
```

## D. DHCP Server-Relay
### Konfigurasi DHCP Server (Jipangu)
Berikut langkah-langkah untuk mengkonfigurasi DHCP Server pada Jipangu:
1. Install DHCP Server dengan command `apt-get update` dan `apt-get install isc-dhcp-server`.
2. Edit file `/etc/default/isc-dhcp-server` dengan menambahkan `eth0` pada bagian INTERFACES.
3. Atur konfigurasi DHCPD untuk semua subnet selain subnet yang mengandung node Doriki dan Jipangu.
4. Jalankan atau restart DHCP server dengan perintah `service isc-dhcp-server restart`.
5. Langkah 3 sampai langkah 5 dapat dilakukan melalui script berikut:
    ```
    # Setup DHCP Server
    echo "
    INTERFACES=\"eth0\"
    " > /etc/default/isc-dhcp-server

    # Setup dhcpd.conf
    echo "
    # Default setting
    ddns-update-style none;
    default-lease-time 600;
    log-facility local7;
    max-lease-time 7200;

    # Blueno
    subnet 10.17.0.0 netmask 255.255.255.128 {
       range 10.17.0.2 10.17.0.126;
       option routers 10.17.0.1;
       option broadcast-address 10.17.0.127;
       option domain-name-servers 10.17.1.10;
       default-lease-time 600;                 
       max-lease-time 7200;                    
    }

    # Cipher
    subnet 10.17.0.128 netmask 255.255.255.128 {
       range 10.17.0.130 10.17.0.254;
       option routers 10.17.0.129;
       option broadcast-address 10.17.0.255;
       option domain-name-servers 10.17.1.10;
       default-lease-time 600;                 
       max-lease-time 7200;                    
    }

    # Elena
    subnet 10.17.4.0 netmask 255.255.254.0 {
       range 10.17.4.2 10.17.5.254;
       option routers 10.17.4.1;
       option broadcast-address 10.17.5.255;
       option domain-name-servers 10.17.1.10;
       default-lease-time 600;                 
       max-lease-time 7200;                    
    }

    # Fukurou
    subnet 10.17.6.0 netmask 255.255.255.0 {
       range 10.17.6.2 10.17.6.254;
       option routers 10.17.6.1;
       option broadcast-address 10.17.6.255;
       option domain-name-servers 10.17.1.10;
       default-lease-time 600;                 
       max-lease-time 7200;                    
    }

    # Jorge dan Maingate
    subnet 10.17.5.8 netmask 255.255.255.248 {
    }
    " > /etc/dhcp/dhcpd.conf;
    
    service isc-dhcp-server restart
    ```

### Konfigurasi DHCP Relay (Water7, Guanhao)
Berikut langkah-langkah untuk mengkonfigurasi DHCP Server pada Water7 dan Guanhao:
1. Install DHCP Relay dengan command `apt-get update` dan `apt-get install isc-dhcp-relay`.
2. Edit file `/etc/default/isc-dhcp-relay` dengan:
    1. menambahkan `eth1` dan `eth3`  pada bagian **INTERFACES**.
    2. menambahkan IP Jipangu, yaitu `10.17.1.11` pada bagian **SERVERS**.  
    ![Konfig DHCP Relay](https://user-images.githubusercontent.com/52129348/144750309-c49e93bb-bd6e-458d-abb7-517521e762a6.png)
3. Jalankan atau restart DHCP Relay dengan perintah `service isc-dhcp-relay restart	`.

### Konfigurasi DHCP Client (Blueno, Cipher, Fukurou, Elena)
Pada file `/etc/network/interfaces`. komen IP Statis pada `eth0` dan ubah `eth0` menjadi seperti berikut:  
```
auto eth0
iface eth0 inet dhcp
```
![Konfigurasi DHCP Client](https://user-images.githubusercontent.com/52129348/144750471-f3fb643a-8fc1-4323-a31e-29fd590d2ec1.png)

