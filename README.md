# Jarkom-Modul-5-A04-2021

**Nama Anggota Kelompok:**
* Julietta Anastasia Rodiah Boru Panjaitan (05111940000033)
* Aulia Eka Putri Aryani (05111940000044)
* Abdun Nafi’ (05111940000066)

Prefix IP: 10.1


## Persyaratan
### Persyaratan 1
Sebelum dapat melakukan iptables, hal pertama yang dapat dilakukan adalah membuat **topologi jaringan**, dimana nantinya Doriki akan berperan sebagai DNS Server, Jipangu sebagai DHCP Server, Maingate dan Jorge sebagai Web Server, Blueno sebagai client dengan jumlah host 100, Cipher sebagai client dengan jumlah host 700, Elena sebagai client dengan jumlah host 300, dan Fukurou sebagai client dengan jumlah host 200.
![a1](https://user-images.githubusercontent.com/76677130/144767844-4c03e136-4634-4fad-802b-564b3b64d0a5.PNG)

### Persyaratan 2
Selanjutnya, menentukan ip melalui **subnetting** dengan **teknik VLSM**.

Pertama, melakukan _labeling_ pada setiap subnet.
![Subnetting-label](https://user-images.githubusercontent.com/76677130/144771032-3e58a873-da02-4c5c-ad50-b89bff1713a4.png)

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

Ketiga, menghitung pembagian IP dengan pohon berdasarkan netmask yang diperoleh melalui perhitungan jumlah ip (/21). 
![Subnetting-tree](https://user-images.githubusercontent.com/76677130/144771035-00dbe5a6-09f2-48f6-a9fb-02ee3aa1ad3b.png)

Keempat, memperoleh hasil dari perhitungan pembagian ip dengan pohon.
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

Kelima, mengatur ip untuk masing-masing interface pada setiap device dengan ip yang telah diperoleh. Namun, khusus untuk client akan diberikan ip dari dhcp.
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
* Blueno (Client)
  ```
  ## A2+
  auto eth0
  iface eth0 inet dhcp
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
* Chiper (Client)
  ```
  ## A3+
  auto eth0
  iface eth0 inet dhcp
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
* Elena (Client)
  ```
  ## A6+
  auto eth0
  iface eth0 inet dhcp
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
* Fukurou (Client)
  ```
  ## A7+
  auto eth0
  iface eth0 inet dhcp
  ```
  ![b1](https://user-images.githubusercontent.com/76677130/144836746-3ccebab4-ca48-442a-bc45-245bfc8708d7.PNG)

### Persyaratan 3
Setelah memperoleh hasil pembagian ip melalui subnetting, dapat dilakukan routing pada router.
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
Selanjutnya, memberikan ip pada subnet _client_ (Blueno, Cipher, Fukurou, dan Elena) secara dinamis menggunakan DHCP Server (Jipangu), dimana router yang menghubungkannya (Water7, Foosha, dan Guanhao) berperan sebagai DHCP Relay.

Pertama, 












