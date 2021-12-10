# Jarkom-Modul-5-A04-2021

**Nama Anggota Kelompok:**
* Julietta Anastasia Rodiah Boru Panjaitan (05111940000033)
* Aulia Eka Putri Aryani (05111940000044)
* Abdun Nafi’ (05111940000066)

Prefix IP: 10.1


## Persyaratan
### Persyaratan 1
Sebelum dapat melakukan iptables, hal pertama yang dapat dilakukan adalah membuat **topologi jaringan**, dimana nantinya Doriki akan berperan sebagai DNS Server, Jipangu sebagai DHCP Server, Maingate dan Jorge sebagai Web Server, Blueno sebagai client dengan jumlah host 100, Cipher sebagai client dengan jumlah host 700, Elena sebagai client dengan jumlah host 300, dan Fukurou sebagai client dengan jumlah host 200.
![a11](https://user-images.githubusercontent.com/76677130/144853283-f37a840c-3d57-40db-85be-5e01195ed54e.PNG)

### Persyaratan 2
Selanjutnya, menentukan ip melalui **subnetting** dengan **teknik VLSM**.

Pertama, melakukan _labelling_ pada setiap subnet.
![subnetting-labelling](https://user-images.githubusercontent.com/76677130/144853361-214542d9-51a9-4488-b00f-06e3065aaa1a.png)

Kedua, menghitung jumlah ip yang dibutuhkan oleh setiap subnet.
| Subnet	| Jumlah IP	| Netmask |
| :---: | :---: | :---: |
| A1	| 3	| /29 |
| A2	| 101	| /25 |
| A3	| 701	| /22 |
| A4	| 2	| /30 |
| A5	| 2	| /30 |
| A6	| 301	| /23 |
| A7	| 201	| /24 |
| A8	| 3	| /29 |
| **Total**	| **1314**	| **/21** |

Ketiga, menghitung pembagian IP dengan pohon berdasarkan netmask yang diperoleh melalui perhitungan jumlah IP (/21). 
![Subnetting-tree](https://user-images.githubusercontent.com/76677130/144771035-00dbe5a6-09f2-48f6-a9fb-02ee3aa1ad3b.png)

Keempat, memperoleh hasil dari perhitungan pembagian IP dengan pohon.
| Subnet	| NID	| Subnet Mask	| Broadcast Address |
| :---: | :---: | :---: | :---: |
| A1	| 10.1.0.8	| 255.255.255.248	| 10.1.0.15 |
| A2	| 10.1.0.128	| 255.255.255.128	| 10.1.0.255 |
| A3	| 10.1.4.0	| 255.255.252.0	| 10.1.7.255 |
| A4	| 10.1.0.0	| 255.255.255.252	| 10.1.0.3 |
| A5	| 10.1.0.4	| 255.255.255.252	| 10.1.0.7 |
| A6	| 10.1.2.0	| 255.255.254.0	| 10.1.3.255 |
| A7	| 10.1.1.0	| 255.255.255.0	| 10.1.1.255 |
| A8	| 10.1.0.16	| 255.255.255.248	| 10.1.0.23 |

Kelima, mengatur IP untuk masing-masing interface pada setiap router dan server dengan IP yang telah diperoleh. IP pada interface client tidak diatur terlebih dahulu karena nantinya akan diatur menggunakan dhcp. Untuk mengatur IP pada interface dapat dilakukan dengan `klik kanan device` > `Configure` > `Edit network configuration`.
* Foosha (Router)
  ```
  ## NAT+
  auto eth0
  iface eth0 inet dhcp
  
  ## A4-
  auto eth1
  iface eth1 inet static
    address 10.1.0.1
    netmask 255.255.255.252
  
  ## A5-
  auto eth2
  iface eth2 inet static
    address 10.1.0.5
    netmask 255.255.255.252
  ```
* Water7 (Router)
  ```
  ## A4+
  auto eth0
  iface eth0 inet static
    address 10.1.0.2
    netmask 255.255.255.252
  
  ## A2-
  auto eth1
  iface eth1 inet static
    address 10.1.0.129
    netmask 255.255.255.128
  
  ## A1-
  auto eth2
  iface eth2 inet static
    address 10.1.0.9
    netmask 255.255.255.248
  
  ## A3-
  auto eth3
  iface eth3 inet static
    address 10.1.4.1
    netmask 255.255.252.0
  ```
* Doriki (Server)
  ```
  ## A1+
  auto eth0
  iface eth0 inet static
    address 10.1.0.10
    netmask 255.255.255.248
    gateway 10.1.0.9
  ```
* Jipangu (Server)
  ```
  ## A1+
  auto eth0
  iface eth0 inet static
    address 10.1.0.11
    netmask 255.255.255.248
    gateway 10.1.0.9
  ```
* Guanhao (Router)
  ```
  ## A5-
  auto eth0
  iface eth0 inet static
    address 10.1.0.6
    netmask 255.255.255.252
  ## A6+
  auto eth1
  iface eth1 inet static
    address 10.1.2.1
    netmask 255.255.254.0
  ## A8+
  auto eth2
  iface eth2 inet static
    address 10.1.0.17
    netmask 255.255.255.248
  ## A7+
  auto eth3
  iface eth3 inet static
    address 10.1.1.1
    netmask 255.255.255.0
  ```
* Jorge (Server)
  ```
  ## A8+
  auto eth0
  iface eth0 inet static
    address 10.1.0.18
    netmask 255.255.255.248
    gateway 10.1.0.17
  ```
* Maingate (Server)
  ```
  ## A8+
  auto eth0
  iface eth0 inet static
    address 10.1.0.19
    netmask 255.255.255.248
    gateway 10.1.0.17
  ```
  ![b1-crop](https://user-images.githubusercontent.com/76677130/144993966-6cc69ddc-e7b9-40d8-99df-934ea2963d72.png)

### Persyaratan 3
Setelah memperoleh hasil pembagian ip melalui subnetting, dapat dilakukan **routing** pada router. 

Sebelum melakukan routing, dapat dilakukan ip forwarding pada setiap router. IP forwarding dilakukan dengan mengubah file `/etc/sysctl.conf` menjadi `net.ipv4.ip_forward=1`. Kemudian, jalankan command `sysctl -p` untuk mengaktifkan perubahan yang dilakukan.
  
Routing pada router dapat dilakukan dengan command berikut ini.
* Foosha
    ```
  ## A4
  route add -net 10.1.0.128 netmask 255.255.255.128 gw 10.1.0.2   # A2
  route add -net 10.1.0.8 netmask 255.255.255.248 gw 10.1.0.2     # A1
  route add -net 10.1.4.0 netmask 255.255.252.0 gw 10.1.0.2       # A3
  ## A5
  route add -net 10.1.2.0 netmask 255.255.254.0 gw 10.1.0.6       # A6
  route add -net 10.1.0.16 netmask 255.255.255.248 gw 10.1.0.6    # A8
  route add -net 10.1.1.0 netmask 255.255.255.0 gw 10.1.0.6       # A7
  ```
* Water7
  ```
  ## Default (A4)
  route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.1.0.1
  ```
* Guanhao
  ```
  ## Default (A5)
  route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.1.0.5
  ```
  ![c1](https://user-images.githubusercontent.com/76677130/144836769-58c3a8ad-7530-48b7-bb1e-4cb2cd53df09.PNG)

### Persyaratan 4
Selanjutnya, memberikan **ip pada subnet client (Blueno, Cipher, Fukurou, dan Elena) secara dinamis** menggunakan DHCP Server (Jipangu), dimana router yang menghubungkannya (Water7, Foosha, dan Guanhao) berperan sebagai DHCP Relay.

**DHCP Server**

Pertama, install dhcp-server pada `Jipangu`.
```
apt-get update
apt-get install isc-dhcp-server -y
```

Kedua, ubah interface pada file `/etc/default/isc-dhcp-server`.
```
INTERFACES="eth0"
```

Ketiga, ubah konfigurasi dhcp pada file `/etc/dhcp/dhcpd.conf`, dimana domain-name-servers mengarah ke Doriki (10.1.0.10) yang berperan sebagai DNS Server.
```
subnet 10.1.0.8 netmask 255.255.255.248 {

}

subnet 10.1.0.128 netmask 255.255.255.128 {
    range 10.1.0.130 10.1.0.230;
    option domain-name-servers 10.1.0.10;
    option routers 10.1.0.129;
    option broadcast-address 10.1.0.255;
    default-lease-time 360;
    max-lease-time 7200;
}

subnet 10.1.4.0 netmask 255.255.252.0 {
    range 10.1.4.2 10.1.6.190;
    option domain-name-servers 10.1.0.10;
    option routers 10.1.4.1;
    option broadcast-address 10.1.7.255;
    default-lease-time 360;
    max-lease-time 7200;
}

subnet 10.1.2.0 netmask 255.255.254.0 {
    range 10.1.2.2 10.1.3.6;
    option domain-name-servers 10.1.0.10;
    option routers 10.1.2.1;
    option broadcast-address 10.1.3.255;
    default-lease-time 360;
    max-lease-time 7200;
}

subnet 10.1.1.0 netmask 255.255.255.0 {
    range 10.1.1.2 10.1.1.202;
    option domain-name-servers 10.1.0.10;
    option routers 10.1.1.1;
    option broadcast-address 10.1.1.255;
    default-lease-time 360;
    max-lease-time 7200;
}
```

Keempat, restart dhcp-server.
```
service isc-dhcp-server restart
```

**DHCP Relay**

Pertama, install dhcp-relay pada roouter yang menghubungkan dhcp-server dengan client, yaitu `Water7`, `Foosha`, dan `Guanhao`.
```
apt-get update
apt-get install isc-dhcp-relay -y
```

Kedua, ubah konfigurasi dhcp-relay pada file `/etc/default/isc-dhcp-relay`, dimana server mengarah ke Jipangu sebagai DHCP Server (10.1.0.11) dan interface menyesuaikan interface yang digunakan untuk relay di setiap routernya, baik dari sumber relay maupun ke tujuan (client atau route relay lainnya). 
* Water7
  ```
  SERVERS="10.1.0.11"
  INTERFACES="eth0 eth1 eth2 eth3"
  OPTIONS=""
  ```
* Foosha
  ```
  SERVERS="10.1.0.11"
  INTERFACES="eth1 eth2"
  OPTIONS=""
  ```
* Guanhao
  ```
  SERVERS="10.1.0.11"
  INTERFACES="eth0 eth1 eth3"
  OPTIONS=""
  ```

Ketiga, start dhcp-relay.
```
service isc-dhcp-relay start
```

**Client**

Pertama, ubah konfigurasi interface dalam file `/etc/network/interfaces` pada client (`Blueno`, `Cipher`, `Fukurou`, dan `Elena`).
```
auto eth0
iface eth0 inet dhcp
```

Kedua, restart node client pada GNS3.



## Soal 1
Pada soal ini diminta untuk menghubungkan topologi dengan jaringan luar melalui Foosha dengan menggunakan iptables tanpa MASQUERADE.

### Pembahasan

## Nomor 1 - keluar
## Foosha
iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 10.1.0.0/21 --to-source 192.168.122.1

### Testing

## Soal 2
Pada soal ini diminta untuk men-drop semua akses HTTP dari luar topologi pada DHCP Server (Doriki) dan DNS Server (Jipangu).

### Pembahasan
## Nomor 2 - drop http pada dns dhcp server
### Foosha
iptables -A FORWARD -d 10.1.0.8/29 -i eth0 -p tcp --dport 80 -j DROP

### Testing

## Soal 3
Pada soal ini diminta untuk membatasi penerimaan koneksi ICMP pada DHCP Server dan DNS Server, dimana kedua server tersebut hanya dapat menerima maksimal 3 koneksi secara bersamaan dan selebihnya di-drop.

### Pembahasan
## Nomor 3 - max 3 koneksi icmp dalam 1 waktu pada dns dhcp server
## Diroki (DNS Server)
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP

## Jipangu (DHCP-Server)
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP

### Testing

## Soal 4
Pada soal ini diminta untuk hanya memperbolehkan akses ke Doriki yang berasal dari subnet Blueno dan Cipher pada pukul 07.00 - 15.00 di hari Senin sampai Kamis, sedangkan waktu lainnya di-reject.

### Pembahasan

### Testing

## Soal 5
Pada soal ini diminta untuk hanya memperbolehkan akses ke Doriki dari subnet Elena dan Fukuro pada pukul 15.01 - 06.59 setiap harinya, sedangkan waktu lainnya di-reject.

### Pembahasan

### Testing

## Soal 6
Pada soal ini diminta untuk mengatur Guanhao sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Web Server Jorge dan Maingate.

### Pembahasan

### Testing

