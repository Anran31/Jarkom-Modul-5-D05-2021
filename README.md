# Jarkom-Modul-5-D05-2021

## Anggota Kelompok D05

| Nama                          | NRP            |
| ----------------------------- | -------------- |
| Andika Nugrahanto             | 05111940000031 |
| Muhammad Rayhan Raffi Pratama | 05111940000110 |
| Fadhil Dimas Sucahyo          | 05111940000212 |

## Soal dan Pembahasan

### A

Soal : Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Luffy dibawah ini

- Jipangu adalah DHCP Server
- Foosha, Water7, dan Guanhao adalah DHCP Relay
- Doriki adalah DNS Server
- Blueno, Cipher, Elena, dan Fukurou adalah client

![topologi gns](./imgs/0.1.PNG)

### B

Soal : Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM.

### Pembagian Subnet

Pembagian subnet menggunakan CIDR. Berdasarkan topologi yang telah dibuat, dapat ditentukan labelling netmask sebagai berikut

![cidr fix-topologi](./imgs/0.2.PNG)

Berikut ini adalah jumlah ip untuk masing-masing subnet yang telah ditentukan.

| Subnet | Jumlah IP | Netmask |
| :----: | :-------: | :-----: |
|   A1   |     3     |   /29   |
|   A2   |    101    |   /25   |
|   A3   |    701    |   /22   |
|   A4   |     2     |   /30   |
|   A5   |     2     |   /30   |
|   A6   |    301    |   /23   |
|   A7   |    201    |   /24   |
|   A8   |     3     |   /29   |

Untuk penggabungan CIDR dapat dilihat di <a href="https://docs.google.com/spreadsheets/d/13OMgRNHS2Clw0rjyUSRwQfsrd-LHccGwSyX0zUvJqDc/edit?usp=sharing">sini</a>

### Pembagian IP

Berikut adalah pembagian IP yang diilustrasikan menggunakan tree. Pada tree berikut dapat diketahui network ID yang dapat digunakan untuk masing-masing subnet.

![cidr fix-tree](./imgs/0.3.PNG)

Berikut adalah tabel pembagian IP untuk subnet A1 hingga A8 yang terdiri dari Network ID, Netmask, dan Broadcast Address. Tabel ini dibuat untuk memudahkan pemberian IP pada GNS3.

![cidr tabel](./imgs/0.4.PNG)

### C

Soal : Setelah melakukan subnetting, kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

Sebelum melakukan routing, membuat konfigurasi untuk masing-masing node pada `Configure > Edit Network Configuration`. Konfigurasi ini dilakukan supaya kita dapat mengetahui IP address dan gateway masing-masing node sehingga dapat saling dihubungkan saat membuat routing. Berikut adalah salah satu konfigurasi yang dilakukan, yaitu pada router Foosha

```
auto eth0
iface eth0 inet static
	address 192.168.122.2
	netmask 255.255.255.252
        gateway 192.168.122.1

auto eth1
iface eth1 inet static
	address 192.194.5.1
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 192.194.12.1
	netmask 255.255.255.252
```

Kemudian, Routing dilakukan antar router pada Foosha, Water7, dan Guanhao

- Foosha

```
route add -net 192.194.0.0 netmask 255.255.248.0 gw 192.194.5.2
route add -net 192.194.8.0 netmask 255.255.248.0 gw 192.194.12.2
```

- Water7

```
route add -net 192.194.4.0 netmask 255.255.255.128 gw 192.194.4.2
route add -net 192.194.4.128 netmask 255.255.255.248 gw 192.194.4.130
route add -net 192.194.4.128 netmask 255.255.255.248 gw 192.194.4.131
route add -net 192.194.0.0 netmask 255.255.252.0 gw 192.194.0.2
```

- Guanhao

```
route add -net 192.194.10.0 netmask 255.255.254.0 gw 192.194.10.2
route add -net 192.194.9.0 netmask 255.255.255.248 gw 192.194.9.2
route add -net 192.194.9.0 netmask 255.255.255.248 gw 192.194.9.3
route add -net 192.194.8.0 netmask 255.255.255.0 gw 192.194.8.2
```

### D

Soal : Tugas berikutnya adalah memberikan IP pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

DHCP Server diletakkan pada Jipangu dengan file konfigurasi `/etc/dhcp/dhcpd.conf`sebagai berikut. Konfigurasi ini digunakan untuk memberikan IP address untuk masing-masing client.

```
subnet 192.194.4.128 netmask 255.255.255.248 {
}

subnet 192.194.4.0 netmask 255.255.255.128 {
        range 192.194.4.2 192.194.4.126;
        option routers 192.194.4.1;
        option broadcast-address 192.194.4.127;
        option domain-name-servers 192.194.4.130;
        default-lease-time 600;
        max-lease-time 600;
}

subnet 192.194.0.0 netmask 255.255.252.0 {
        range 192.194.0.2 192.194.3.254;
        option routers 192.194.0.1;
        option broadcast-address 192.194.3.255;
        option domain-name-servers 192.194.4.130;
        default-lease-time 600;
        max-lease-time 600;
}

subnet 192.194.10.0 netmask 255.255.254.0 {
        range 192.194.10.2 192.194.11.254;
        option routers 192.194.10.1;
        option broadcast-address 192.194.11.255;
        option domain-name-servers 192.194.4.130;
        default-lease-time 600;
        max-lease-time 600;
}

subnet 192.194.8.0 netmask 255.255.255.0 {
        range 192.194.8.2 192.194.8.254;
        option routers 192.194.8.1;
        option broadcast-address 192.194.8.255;
        option domain-name-servers 192.194.4.130;
        default-lease-time 600;
        max-lease-time 600;
}
```

Supaya client dapat terhubung dengan internet, pada file konfigurasi `/etc/dhcp/dhcpd.conf` diarahkan menuju `eth0` yaitu interface yang mengarah ke router. Kemudian, menambahkan file konfigurasi `/etc/default/isc-dhcp-relay` untuk DHCP Relay pada router Water7, Foosha, dan Guanhao sebagai berikut

```
SERVERS="192.194.4.131"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
```

IP `192.194.4.131` adalah IP Address dari DHCP Server yaitu Jipangu, lalu mengarahkan interface pada `eth0 eth1 eth2 eth3` supaya DHCP Relay dapat meneruskan DHCP request pada DHCP Server

### No 1

Soal :
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

Jawaban :

Pada Foosha ditambahkan rule Iptables di bawah ini:

```bash
iptables -t nat -A POSTROUTING -s 192.194.0.0/20 -o eth0 -j SNAT --to-source 192.168.122.2
```

Selain itu, agar klien dapat mengakses ke luar, maka perlu ditambahkan forwarding pada DNS Server yaitu **Doriki** pada file `/etc/bind/named.conf.options`, uncomment pada bagian (0.0.0.0 diubah menjadi 192.168.122.1):

```
forwarders {
    192.168.122.1;
};
```

Comment pada bagian ini

```
// dnssec-validation auto;
```

Dan tambahkan

```
allow-query{any;};
```

Kemudian tes dengan `ping google.com` pada salah satu klien
![1.1](./imgs/1.1.PNG)

### No 2

Soal :
Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.

Jawaban :
Pada Foosha ditambahkan rule Iptables di bawah ini:

```bash
iptables -A FORWARD -d 192.194.4.128/29 ! -s 192.194.0.0/20 -i eth0 -p tcp --dport 80 -j DROP
```
