# Jarkom-Modul-5-A02-2023


<table>
    <tr>
        <th colspan=2> Kelompok A02 </th>
    </tr>
    <tr>
        <th>NRP</th>
        <th>Nama Anggota</th>
    </tr>
    <tr>
        <td>5025211032</td>
        <td>Nabila A'idah Diani</td>
    </tr>
</table>



# Topologi

Berikut merupakan topologi yang digunakan untuk praktikum modul 5

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/aaf582ed-19e3-4ee8-8763-bd41394f337f)



# Prefix IP

Berikut merupakan prefix ip yang digunakan oleh kelompok saya: `10.0.x.x`



# Rute

Berikut merupakan rute yang digunakan untuk praktikum modul 5

```
AURA
#bawah
route add -net 10.0.0.8 netmask 255.255.255.252 gw 10.0.0.17
route add -net 10.0.2.0 netmask 255.255.254.0 gw 10.0.0.17
route add -net 10.0.0.128 netmask 255.255.255.128 gw 10.0.0.17
route add -net 10.0.0.12 netmask 255.255.255.252 gw 10.0.0.17
route add -net 10.0.0.4 netmask 255.255.255.252 gw 10.0.0.17
route add -net 10.0.0.0 netmask 255.255.255.252 gw 10.0.0.17

#kanan
route add -net 10.0.8.0 netmask 255.255.248.0 gw 10.0.0.21
route add -net 10.0.4.0 netmask 255.255.252.0 gw 10.0.0.21

FRIEREN
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.0.0.18
route add -net 10.0.2.0 netmask 255.255.254.0 gw 10.0.0.9
route add -net 10.0.0.128 netmask 255.255.255.128 gw 10.0.0.9
route add -net 10.0.0.4 netmask 255.255.255.252 gw 10.0.0.9
route add -net 10.0.0.0 netmask 255.255.255.252 gw 10.0.0.9

HIMMEL
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.0.0.10
route add -net 10.0.0.4 netmask 255.255.255.252 gw 10.0.0.129
route add -net 10.0.0.0 netmask 255.255.255.252 gw 10.0.0.129

FERN
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.0.0.130

HEITER
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.0.0.21
```


# Konfigurasi

Berikut merupakan konfigurasi yang digunakan pada node DHCP

```
DHCP Server
apt update
apt install isc-dhcp-server -y
echo ‘INTERFACESv4=”eth0”’ > /etc/default/isc-dhcp-server
service isc-dhcp-server start

echo ‘
subnet 10.0.0.0 netmask 255.255.255.252 {
}
subnet 10.0.0.4 netmask 255.255.255.252 {
}

subnet 10.0.0.8 netmask 255.255.255.252 {
}

subnet 10.0.0.12 netmask 255.255.255.252 {
}

subnet 10.0.0.16 netmask 255.255.255.252 {
}

subnet 10.0.0.20 netmask 255.255.255.252 {
}

subnet 10.0.0.128 netmask 255.255.255.128 {
range 10.0.0.131 10.0.0.254;
option routers 10.0.0.129;
option broadcast-address 10.0.0.255;
option domain-name-servers 10.0.0.6;
default-lease-time 180;
max-lease-time 5760;	
}

subnet 10.0.2.0 netmask 255.255.254.0 {
range 10.0.2.2 10.0.3.254;
option routers 10.0.2.1;
option broadcast-address 10.0.3.255;
option domain-name-servers 10.0.0.6;
default-lease-time 180;
max-lease-time 5760;	
}

subnet 10.0.8.0 netmask 255.255.248.0 {
range 10.0.8.2 10.0.15.254;
option routers 10.0.8.1;
option broadcast-address 10.0.15.255;
option domain-name-servers 10.0.0.6;
default-lease-time 180;
max-lease-time 5760;	
}

subnet 10.0.4.0 netmask 255.255.252.0 {
range 10.0.4.3 10.0.7.254;
option routers 10.0.4.1;
option broadcast-address 10.0.7.255;
option domain-name-servers 10.0.0.6;
default-lease-time 180;
max-lease-time 5760;	
}’ >  /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart

DHCP Relay
apt update
apt install isc-dhcp-relay -y
SERVERS=”10.0.0.2”
INTERFACES=”” // Tergantung ada berapa eth

echo ‘
net.ipv4.ip_forward=1’ > /etc/sysctl.conf

service isc-dhcp-relay start
```


# Soal

## Nomor 1

Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Aura menggunakan iptables, tetapi tidak ingin menggunakan MASQUERADE.

### Jawaban

```
IPETH0="$(ip -br a | grep eth0 | awk '{print $NF}' | cut -d'/' -f1)"
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source "$IPETH0" -s 10.0.0.0/20
```


## Nomor 2

Kalian diminta untuk melakukan drop semua TCP dan UDP kecuali port 8080 pada TCP.

### Jawaban

```
iptables -A INPUT -p tcp –dport 8080 -j ACCEPT
iptables -A INPUT -p tcp ! --dport 8080 -j DROP
iptables -A INPUT -p udp -j DROP
```

### Hasil
#### Di TCP port 8080

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/c9fed51a-5773-48e1-a5df-e54499fd0652)

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/960a2cc0-3999-42d5-9297-422f86c417da)

#### Di TCP selain port 8080

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/ac0fbe40-12b5-4a4b-986a-905bad000c19)

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/b2072734-de35-4c37-b463-43deac798a31)

#### Di UDP

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/546a068a-c99b-4b37-81c0-57c8368bcae7)

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/ce5c1757-0f68-4d82-80bb-d14fb746205f)


## Nomor 3

Kepala Suku North Area meminta kalian untuk membatasi DHCP dan DNS Server hanya dapat dilakukan ping oleh maksimal 3 device secara bersamaan, selebihnya akan di drop.

### Jawaban

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

### Hasil

Revolte (DHCP Server)

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/fe97b48b-fe18-4e03-b30a-2f237cb93f57)

Richter (DNS Server)

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/61099ef8-38fa-4dfd-a271-8c57cc84aaa5)


## Nomor 4

Lakukan pembatasan sehingga koneksi SSH pada Web Server hanya dapat dilakukan oleh masyarakat yang berada pada GrobeForest.

### Jawaban

```
iptables -A INPUT -p tcp --dport 22 -s 10.0.4.0/22 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j DROP
```

### Hasil

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/17a99d0f-1c09-4d35-be93-1a14c762d328)


## Nomor 5

Selain itu, akses menuju WebServer hanya diperbolehkan saat jam kerja yaitu Senin-Jumat pada pukul 08.00-16.00

### Jawaban

```
iptables -A INPUT  -m time --weekdays Mon,Tue,Wed,Thu,Fri --timestart 08:00 --timestop 16:00 -p tcp --dport 22 -s 10.0.4.0/22 -j ACCEPT
```

### Hasil

Saat weekday:

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/0ca2b744-8e13-4b10-9baa-59aae5ff81e1)

Saat weekend:

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/41116ff2-00da-4a80-b38d-4d4764ca7ad9)



## Nomor 6

Lalu, karena ternyata terdapat beberapa waktu di mana network administrator dari WebServer tidak bisa stand by, sehingga perlu ditambahkan rule bahwa akses pada hari Senin - Kamis pada jam 12.00 - 13.00 dilarang (istirahat maksi cuy) dan akses di hari Jumat pada jam 11.00 - 13.00 juga dilarang (maklum, Jumatan rek)

### Jawaban

```
iptables -A INPUT  -m time --weekdays Mon,Tue,Wed,Thu --timestart 12:00 --timestop 13:00 -p tcp --dport 22 -s 10.0.4.0/22 -j DROP
iptables -A INPUT  -m time --weekdays Fri --timestart 11:00 --timestop 13:00 -p tcp --dport 22 -s 10.0.4.0/22 -j DROP
iptables -A INPUT  -m time --weekdays Mon,Tue,Wed,Thu,Fri --timestart 08:00 --timestop 16:00 -p tcp --dport 22 -s 10.0.4.0/22 -j ACCEPT
```

### Hasil

![image](https://github.com/nabilaaidah/Jarkom-Modul-5-A02-2023/assets/110476969/1cfe3a66-192a-4a0c-b678-1c0265fd3d6b)
