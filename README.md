# LAB-26-DST-NAT
tanggal 17 Agustus 2025

![m](<img width="657" height="309" alt="image" src="https://github.com/user-attachments/assets/23ee2aa5-1922-417c-8619-0cfc90ad4a63" />
)

seperti yang kita baca, PC1 di jaringan kiri meminta agar bisa   
akses Web Server 2 lewat NAT di Router, dan Begitu sebaliknya.

# Langkah Konfigurasi di Mikrotik

1. Konfigurasi Router R1 (agar PC1 bisa akses Web Server 2)

```
/ip firewall nat add chain=dstnat \
   dst-address=IP_Public_R1 \
   protocol=tcp dst-port=80 \
   action=dst-nat to-addresses=192.168.200.200 to-ports=80
```

2. Konfigurasi Router R2 (agar PC2 bisa akses Web Server 1)

```
/ip firewall nat add chain=dstnat \
   dst-address=IP_Public_R2 \
   protocol=tcp dst-port=80 \
   action=dst-nat to-addresses=192.168.100.100 to-ports=80
```

**Keterangan:**

- `dst-address` = alamat publik/interface WAN router yang dituju.
- `dst-port=80` = karena layanan Web Server biasanya di port 80 (HTTP).
- `to-addresses` = IP private server tujuan.
-  `to-ports` = port layanan pada server.

---

3. Buat Chain Forward Firewall Rule    
   Supaya trafik bisa diteruskan ke server, tambahkan rule di **filter**:  
R1
```
/ip firewall filter add chain=forward \
   dst-address=192.168.200.200 protocol=tcp dst-port=80 \
   action=accept.
```
R2
```
/ip firewall filter add chain=forward \
   dst-address=192.168.100.100 protocol=tcp dst-port=80 \
   action=accept
```

---

# pengujian 
Uji koneksi dari PC1 ke Web Server 2, dan dari PC2 ke Web Server 1
via browser: http://<IP_Router_Public>

![m]()

# Kesimpulan
Dengan menggunakan **DST-NAT (PAT)** pada Mikrotik, trafik dari jaringan luar dapat diarahkan       
ke server internal dengan aman. Hal ini memungkinkan server dengan IP private tetap bisa   
diakses dari luar melalui IP publik router, tanpa harus mengubah konfigurasi IP server.   
