# Jarkom-Modul5-C06-2021

## Anggota Kelompok : 
- 05111940000004 | Nizar Mayraldo
- 05111940000087 | William Handi Wijaya
- 05111940000202 | Muhammad Naufaldillah

## A. Topologi Jaringan
![Topologi Jaringan](https://user-images.githubusercontent.com/52129348/144748505-68a8193c-c485-4b07-9787-bae9b98ce504.png)

## B. Subnetting (CIDR)
![topologi1](https://user-images.githubusercontent.com/52129348/144882291-c6ca2a3b-6fe6-469b-aefc-b4dd583c4f3b.png)  
![topologi2](https://user-images.githubusercontent.com/52129348/144882313-75bb7be0-38ea-4605-a42e-842c535674d2.png)  
![topologi3](https://user-images.githubusercontent.com/52129348/144882322-75ed958b-6e8b-4290-8357-a8305d607ee0.png)  
![Pohon IP](https://user-images.githubusercontent.com/52129348/144882353-74ae71aa-3280-4a2b-8688-fb583233c6a3.jpg)  

Subnetting akan dilakukan menggunakan teknik CIDR seperti pada gambar di atas. Kemudian, karena setiap subnet sudah mendapatkan pembagian IP, berikut ini adalah pengaturan IP pada file `/etc/network/interfaces` untuk masing-masing nodes pada topologi:

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
	address 10.17.4.1
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
	address 10.17.4.2
	netmask 255.255.255.252
    
# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.17.6.1
	netmask 255.255.254.0
    
# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.17.4.9
	netmask 255.255.255.248
    
# Static config for eth3
auto eth3
iface eth3 inet static
	address 10.17.5.1
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
	address 10.17.4.11
	netmask 255.255.255.252
	gateway 10.17.4.9
```

### Jorge (Web Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.4.10
	netmask 255.255.255.252
	gateway 10.17.4.9
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
	address 10.17.6.2
	netmask 255.255.254.0
	gateway 10.17.6.1
```

### Fukurou (Client dengan 200 host)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.17.5.2
	netmask 255.255.255.0
	gateway 10.17.5.1
```

## C. Routing (CIDR)
Berdasarkan topologi pada soal A, karena menggunakan teknik subneting CIDR, maka routing yang diperlukan lebih sederhana. Routing hanya perlu dilakukan di node Foosha seperti berikut:
```
route add -net 10.17.0.0 netmask 255.255.254.0 gw 10.17.1.2
route add -net 10.17.4.0 netmask 255.255.252.0 gw 10.17.4.2
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
    log-facility local7;
    default-lease-time 600;
    max-lease-time 7200;

    # Blueno
    subnet 10.17.0.0 netmask 255.255.255.128 {
       range 10.17.0.2 10.17.0.126;
       option routers 10.17.0.1;
       option broadcast-address 10.17.0.127;
       option domain-name-servers 10.17.1.10;           
    }

    # Cipher
    subnet 10.17.0.128 netmask 255.255.255.128 {
       range 10.17.0.130 10.17.0.254;
       option routers 10.17.0.129;
       option broadcast-address 10.17.0.255;
       option domain-name-servers 10.17.1.10;                 
    }

    # Elena
    subnet 10.17.6.0 netmask 255.255.254.0 {
       range 10.17.6.2 10.17.7.254;
       option routers 10.17.6.1;
       option broadcast-address 10.17.7.255;
       option domain-name-servers 10.17.1.10;                  
    }

    # Fukurou
    subnet 10.17.5.0 netmask 255.255.255.0 {
       range 10.17.5.2 10.17.5.254;
       option routers 10.17.5.1;
       option broadcast-address 10.17.5.255;
       option domain-name-servers 10.17.1.10;                   
    }

    # Doriki dan Jipangu
    subnet 10.17.1.8 netmask 255.255.255.248 {
    }

    # Jorge dan Maingate
    subnet 10.17.4.8 netmask 255.255.255.248 {
    }
    " > /etc/dhcp/dhcpd.conf;

    service isc-dhcp-server restart
    ```

### Konfigurasi DHCP Relay (Water7, Guanhao, Foosha)
Berikut langkah-langkah untuk mengkonfigurasi DHCP Relay:
1. Install DHCP Relay dengan command `apt-get update` dan `apt-get install isc-dhcp-relay`.
2. Edit file `/etc/default/isc-dhcp-relay` pada:
    1. Water7
        ```
        INTERFACES="eth0 eth1 eth2 eth3"
        SERVERS="10.17.1.11"
        ```
    2. Foosha
        ```
        INTERFACES="eth1 eth2"
        SERVERS="10.17.1.11"
        ```
    3. Guanhao
        ```
        INTERFACES="eth0 eth1 eth3"
        SERVERS="10.17.1.11"
        ```
3. Jalankan atau restart DHCP Relay dengan perintah `service isc-dhcp-relay restart`.

### Konfigurasi DHCP Client (Blueno, Cipher, Fukurou, Elena)
Pada file `/etc/network/interfaces`. komen IP Statis pada `eth0` dan ubah `eth0` menjadi seperti berikut:  
```
auto eth0
iface eth0 inet dhcp
```
![Konfigurasi DHCP Client](https://user-images.githubusercontent.com/52129348/144750471-f3fb643a-8fc1-4323-a31e-29fd590d2ec1.png)

## 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
Pada **Foosha**, tuliskan perintah:  
```
iptables -t nat -A POSTROUTING -s 10.17.0.0/16 -o eth0 -j SNAT --to-source <ip DHCP di Foosha>
```  
![Bukti no.1](https://user-images.githubusercontent.com/52129348/144869323-725871ac-1efe-493f-a8ef-d1be3be40b7c.gif)


## 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.
Pada **Foosha**, tuliskan perintah berikut:  
```
iptables -A FORWARD -d 10.17.1.8/29 -p tcp --dport 80 -i eth0 -j DROP
```  

## 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.
Pada **Jipangu** (DHCP Server) dan **Doriki** (DNS Server), tuliskan perintah berikut:  
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```  
![Bukti no.3](https://user-images.githubusercontent.com/52129348/144869506-a34e4b8e-fc8b-44c6-9a03-adfa3f1f7010.gif)

## 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.
Pada **Water7**, tulis perintah ini:  
```
iptables -A FORWARD -s 10.17.0.0/24 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A FORWARD -s 10.17.0.0/24 -j REJECT
```

## 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.
Pada **Guanhao**, tulis perintah ini:  
```
iptables -A FORWARD -s 10.17.6.0/23,10.17.5.0/24 -m time --timestart 15:01 --timestop 23:59 -j ACCEPT
iptables -A FORWARD -s 10.17.6.0/23,10.17.5.0/24 -m time --timestart 00:00 --timestop 06:59 -j ACCEPT
iptables -A FORWARD -s 10.17.6.0/23,10.17.5.0/24 -j REJECT
```

## 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate.
Pada **Guanhao**, tulis perintah berikut:
```
iptables -t nat -A PREROUTING -d 10.17.4.8 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.17.4.10
iptables -t nat -A PREROUTING -d 10.17.4.8 -j DNAT --to-destination 10.17.4.11
iptables -t nat -A POSTROUTING -d 10.17.4.10,10.17.4.11 -j SNAT --to-source 10.17.4.8
```  

Pada **Jorge** dan **Maingate** yang merupakan Web Server, install apache2 dengan perintah:
```
apt-get update
apt-get install apache2
```

Pada DNS Server, yaitu **Doriki**, install bind9 dengan perintah:
```
apt-get update
apt-get install bind9
```
Setelah itu, edit `/etc/bind/named.conf.local` dengan domain `jarkomC06.com` seperti berikut:
```
zone "jarkomC06.com" {
    type master;
    file "/etc/bind/jarkom/jarkomC06.com";
};
```
Kemudian, buat folder baru dengan perintah `mkdir /etc/bind/jarkom` dan pindahkan `db.local` ke folder yang baru saja dibuat dengan nama `jarkomC06.com`. Langkah tersebut dapat dilakukan dengan perintah `cp /etc/bind/db.local /etc/bind/jarkom/jarkomC06.com`. Kemudian, edit file `jarkomC06.com` tersebut sehingga pointer A menjadi IP `10.17.4.8` dan nama domain, nameserver, serta CNAME menjadi `jarkomC06.com`. Setelah itu, restart bind9 dengan perintah `service bind9 restart`.  

Langkah-langkah di atas bisa dijalnkan melalui script berikut:
```
# Daftarkan domain name baru
echo "
zone \"jarkomC06.com\" {
        type master;
        file \"/etc/bind/jarkom/jarkomC06.com\";
};
" > /etc/bind/named.conf.local

# Buat folder jarkom dan file yang menyimpan konfigurasi DNS
[ ! -d "/etc/bind/jarkom" ] && mkdir /etc/bind/jarkom
cp /etc/bind/db.local /etc/bind/jarkom/jarkomC06.com

# Buat konfigurasi DNS
echo "
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     jarkomC06.com. root.jarkomC06.com. (
                     2021100401         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      jarkomC06.com.
@       IN      A       10.17.4.8
www     IN      CNAME   jarkomC06.com.
" > /etc/bind/jarkom/jarkomC06.com

# Restart bind9
service bind9 restart
```
