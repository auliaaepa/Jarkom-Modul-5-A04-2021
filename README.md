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
  
Routing pada router dapat dilakukan dengan menuliskan command berikut ini.
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

Test routing dengan saling ping ip device satu sama lain
![c2](https://user-images.githubusercontent.com/76677130/145600858-5ca17fa3-7b2a-4400-9054-e4455a611f35.PNG)

### Persyaratan 4
Selanjutnya, memberikan **ip pada subnet client (Blueno, Cipher, Fukurou, dan Elena) secara dinamis** menggunakan DHCP Server (Jipangu), dimana router yang menghubungkannya (Water7, Foosha, dan Guanhao) berperan sebagai DHCP Relay.

**Foosha**
Agar dapat menginstall DHCP Server dan DHCP Relay, maka jaringan lokal harus terhubung dengan jaringan luar terlebih dahulu. Untuk menghubungkannya, dapat dilakukan iptables pada foosha.
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.1.0.0/21
```

**DHCP Server**

Pertama, sebagai langkah lanjutan menghubungkan dengan jaringan luar, ubah file `/etc/resolv.conf` pada `Jipangu` dengan name server pada Foosha.
```
nameserver 192.168.122.1 
```

Kedua, install dhcp-server.
```
apt-get update
apt-get install isc-dhcp-server -y
```

Ketiga, ubah interface pada file `/etc/default/isc-dhcp-server`.
```
INTERFACES="eth0"
```

Keempat, ubah konfigurasi dhcp pada file `/etc/dhcp/dhcpd.conf`, dimana domain-name-servers mengarah ke Doriki (10.1.0.10) yang berperan sebagai DNS Server.
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

Kelima, restart dhcp-server.
```
service isc-dhcp-server restart
```

**DHCP Relay**

Pertama, sama seperti DHCP Server, untuk menghubungkan DHCP Relay dengan jaringan luar, ubah file `/etc/resolv.conf` dengan name server pada Foosha.
```
nameserver 192.168.122.1 
```

Kedua, install dhcp-relay pada roouter yang menghubungkan dhcp-server dengan client, yaitu `Water7`, `Foosha`, dan `Guanhao`.
```
apt-get update
apt-get install isc-dhcp-relay -y
```

Ketiga, ubah konfigurasi dhcp-relay pada file `/etc/default/isc-dhcp-relay`, dimana server mengarah ke Jipangu sebagai DHCP Server (10.1.0.11) dan interface menyesuaikan interface yang digunakan untuk relay di setiap routernya, baik dari sumber relay maupun ke tujuan (client atau route relay lainnya). 
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

Keempat, start dhcp-relay.
```
service isc-dhcp-relay start
```

**Client**

Pertama, ubah konfigurasi interface dalam file `/etc/network/interfaces` pada client (`Blueno`, `Cipher`, `Fukurou`, dan `Elena`).
```
auto eth0
iface eth0 inet dhcp
```

Kedua, `restart node client` pada GNS3.

Ketiga, cek ip yang diperoleh setiap client dengan command `ip a`.
![d1](https://user-images.githubusercontent.com/76677130/145604385-622ff4aa-74ca-4394-b08d-1c6c882c06de.PNG)

## Soal 1
Pada soal ini diminta untuk menghubungkan topologi dengan jaringan luar melalui Foosha dengan menggunakan iptables tanpa MASQUERADE.

### Pembahasan
Karena sebelumnya telah menggunakan iptables dengan MASQUERADE, maka untuk menghapus pengaturan iptables yang telah ada pada Foosha, hal pertama yang harus dilakukan adalah melakukan `restart node` pada `Foosha`. Kemudian, hal selanjutnya yang dilakukan adalah mengulang `routing` dan `menjalankan dhcp-relay` pada Foosha. Hal ini dikarenakan pengaturan routing pada Foosha ikut terhapus saat node Foosha di-restart. Bagitu pula dengan status service dhcp-relay yang berubah menjadi stop.

Setelah semuanya telah disetting, selanjutnya melakukan iptables pada Foosha.
```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 10.1.0.0/21 --to-source 192.168.122.203
```
![1b](https://user-images.githubusercontent.com/76677130/145607399-4e8e3a47-a17e-4e9e-bb2e-faaa503d1aa0.PNG)
Dimana 192.168.122.203 merupakan IP Foosha yang diperoleh melalui dhcp pada NAT. IP Foosha dapat dicek melalui `ip a`.
![1a](https://user-images.githubusercontent.com/76677130/145607356-c7cad198-0be0-42a2-8fd2-6ed3d3d54032.PNG)

Agar router dan server lainnya dapat ikut terhubung dengan jaringan luar, maka name server pada file `/etc/resolv.conf` harus diubah dengan name server pada Foosha, terutama pada `Doriki`, `Jorge`, dan `Maingate` yang belum sempat diubah.
```
nameserver 192.168.122.1 
```

Sedangkan untuk client, harus dilakukan melalui DNS Server (`Doriki`), sebagaimana domain-name-servers yang diberikan melalui dhcp-relay.
* Install bind9
  ```
  apt-get update
  apt-get install bind9 -y
  ```
* Ubah file `/etc/bind/named.conf.options` untuk men-forward ke name server Foosha
  ```
  options {
      directory "/var/cache/bind";

      forwarders {
          192.168.122.1;
      };

      allow-query{ any; };

      auth-nxdomain no;    # conform to RFC1035
      listen-on-v6 { any; };
  };
  ```
* Restart bind9
  ```
  service bind9 restart
  ```

### Testing
Testing dapat dilakukan dengan ping google.com pada device.
* Foosha
![1t1](https://user-images.githubusercontent.com/76677130/145609649-edd1a8fb-6bf2-4e72-8717-b1861d740c99.PNG)
* Blueno
![1t2](https://user-images.githubusercontent.com/76677130/145609707-d5034b27-63e2-43c7-b837-392f1520e709.PNG)


## Soal 2
Pada soal ini diminta untuk men-drop semua akses HTTP dari luar topologi pada DHCP Server (Jipangu) dan DNS Server (Doriki).

### Pembahasan
Untuk dapat men-drop akses HTTP dari luar topologi, maka iptables dapat diterapkan pada `Foosha`, dimana dport yang digunakan adalah 80 (port HTTP).
```
iptables -A FORWARD -d 10.1.0.8/29 -i eth0 -p tcp --dport 80 -j DROP
```
![2a](https://user-images.githubusercontent.com/76677130/145617189-91e50b85-0b63-4b22-925c-8a9d2e7fabca.PNG)

### Testing
Testing dapat dilakukan dengan cara sebagai berikut.
* Install netcat pada `Jipangu`
* Ketik command berikut pada Jipangu
  ```
  nc -l -p 80
  ```
* Scanning port 80 yang menuju Jipangu (10.1.0.11) dengan command berikut pada `Foosha` (hasil masih salah)
  ```
  nmap -p 80 10.1.0.11
  ```
![2t1'](https://user-images.githubusercontent.com/76677130/145615894-6d11c698-39c7-4fcb-bf58-aa18f04dee50.PNG)


## Soal 3
Pada soal ini diminta untuk membatasi penerimaan koneksi ICMP pada DHCP Server (Jipangu) dan DNS Server (Doriki), dimana kedua server tersebut hanya dapat menerima maksimal 3 koneksi secara bersamaan dan selebihnya di-drop.

### Pembahasan
Untuk membatasi 3 koneksi ICMP dalam waktu yang bersamaan dapat dilakukan iptables berikut pada `Doriki` dan `Jipangu`. 
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
![3a](https://user-images.githubusercontent.com/76677130/145617210-50f940c2-55fe-4a94-8e90-06d75f6e858f.PNG)
![3b](https://user-images.githubusercontent.com/76677130/145617231-e9452cc5-2d3b-475a-a224-90c0748b77d4.PNG)

### Testing
Testing dapat dilakukan dengan melakukan ping ke `Jipangu` (10.1.0.11) pada lebih dari 3 device yang berbeda. Jika ping pada device ke-4 gagal, maka iptables berhasil.
![3t1](https://user-images.githubusercontent.com/76677130/145617353-00dd9575-88d2-4220-b998-1a744ac836c5.PNG)


## Soal 4
Pada soal ini diminta untuk hanya memperbolehkan akses ke Doriki yang berasal dari subnet Blueno dan Cipher pada pukul 07.00 - 15.00 di hari Senin sampai Kamis, sedangkan waktu lainnya di-reject.

### Pembahasan
Untuk hanya memperbolehkan akses ke Doriki dari subnet Blueno (10.1.0.128/25) dan Cipher (10.1.4.0/22) pada waktu tersebut, dapat dilakukan iptables berikut pada `Doriki`.
```
iptables -A INPUT -s 10.1.0.128/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.1.0.128/25 -j REJECT 
iptables -A INPUT -s 10.1.4.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 10.1.4.0/22 -j REJECT
```
![4a](https://user-images.githubusercontent.com/76677130/145617956-c6e84b67-fdad-43bd-9813-a51a8ac00fc0.PNG)

### Testing
Testing dapat dilakukan dengan mengubah tanggal dan waktu pada `Blueno` atau `Chiper` dan melakukan ping ke Doriki (10.1.0.10).
* Pada waktu yang diperbolehkan
![4t1](https://user-images.githubusercontent.com/76677130/145618412-1f97a96a-5e24-4ffb-81a6-194ae0f4cf28.PNG)
* Pada waktu yang tidak diperbolehkan
![4t3](https://user-images.githubusercontent.com/76677130/145618428-380a6e89-eb97-4939-9da2-770a447c62b4.PNG)


## Soal 5
Pada soal ini diminta untuk hanya memperbolehkan akses ke Doriki dari subnet Elena dan Fukuro pada pukul 15.01 - 06.59 setiap harinya, sedangkan waktu lainnya di-reject.

### Pembahasan
Untuk hanya memperbolehkan akses ke Doriki dari subnet Elena (10.1.2.0/23) dan Fukurou (10.1.1.0/24) pada waktu tersebut, dapat dilakukan iptables berikut pada `Doriki`.
```
iptables -A INPUT -s 10.1.2.0/23 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 10.1.2.0/23 -j REJECT
iptables -A INPUT -s 10.1.1.0/24 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 10.1.1.0/24 -j REJECT
```
![5a](https://user-images.githubusercontent.com/76677130/145618861-167cef92-ce61-48ed-b2e5-eba22bc5e131.PNG)

### Testing
Testing dapat dilakukan dengan mengubah tanggal dan waktu pada `Elena` atau `Fukurou` dan melakukan ping ke Doriki (10.1.0.10).
* Pada waktu yang diperbolehkan
![5t2](https://user-images.githubusercontent.com/76677130/145619085-8763d42f-b458-4578-bead-95d647b01e76.PNG)
* Pada waktu yang tidak diperbolehkan
![5t1](https://user-images.githubusercontent.com/76677130/145619095-5ae3ad23-6201-4347-a777-f5f9bc47bbc9.PNG)


## Soal 6
Pada soal ini diminta untuk mengatur Guanhao sehingga setiap request dari client yang mengakses DNS Server Diriki akan didistribusikan secara bergantian pada Web Server Jorge dan Maingate.

### Pembahasan
Untuk mendistribusikan request DNS Server (10.1.0.10) pada Web Server Jorge (10.1.0.18) dan Maingate (10.1.0.19), dapat dilakukan iptables sebagai berikut, dimana port 80 merupakan port default untuk web server.
```
iptables -t nat -A PREROUTING -p tcp -d 10.1.0.10 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.1.0.18:80
iptables -t nat -A PREROUTING -p tcp -d 10.1.0.10 -j DNAT --to-destination 10.1.0.19:80
iptables -t nat -A POSTROUTING -p tcp -d 10.1.0.18 --dport 80 -j SNAT --to-source 10.1.0.10
iptables -t nat -A POSTROUTING -p tcp -d 10.1.0.19 --dport 80 -j SNAT --to-source 10.1.0.10
```
![6a](https://user-images.githubusercontent.com/76677130/145619608-d25d8390-31ff-4410-9649-3f5aed19effa.PNG)

### Testing
Testing dapat dilakukan dengan cara sebagai berikut.
* Install netcat pada `Jorge`, `Maingate`, serta client
  ```
  apt-get update
  apt-get install netcat -y
  ```
* Ketik command berikut pada `Jorge` dan `Maingate` untuk membaca dari koneksi tcp degan port 90
  ```
  nc -l -p 80
  ```
* Ketik command berikut pada client (`Fukurou`) untuk menulis (me-request) ke DNS Server (10.1.0.10) melalui koneksi tcp dengan port 80
  ```
  nc 10.1.0.10 80
  ```
* Tulis apapun pada netcat di Fukurou
* Hentikan netcat pada Fukurou dan lakukan ulang, jika request terdistribusi pada Web Server Jorge dan Maingate maka berhasil
![6t1](https://user-images.githubusercontent.com/76677130/145620843-e773857e-7533-4ea5-9076-aa5daea427be.PNG)


## Kendala
Kendala yang dialamai selama pengerjaan modul ini adalah sebagai berikut.
* Men-drop HTTP dari jaringan luar ke Doriki dan Jipangu gagal dilakukan (open, bukan filtered) 
