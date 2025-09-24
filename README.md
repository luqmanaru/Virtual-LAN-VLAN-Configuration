# Virtual-LAN-VLAN-Configuration

Dokumentasi lengkap konfigurasi VLAN untuk tugas pertemuan kesembilan, termasuk pembuatan VLAN, konfigurasi trunk, dan implementasi DHCP server.

## Deskripsi

Tugas ini bertujuan untuk mengimplementasikan jaringan VLAN dengan konfigurasi DHCP server untuk tiga departemen: Marketing, Sales, dan Finance. Implementasi dilakukan menggunakan tiga switch (SW-1, SW-3, SW-4) dan satu router (R1) yang berfungsi sebagai DHCP server untuk setiap VLAN.

## Topologi Jaringan

```
        +------+
        |  R1  |
        +------+
           |
           |
        +------+
        | SW-4 |
        +------+
       /   |   \
      /    |    \
 +------+ +------+ +------+
 | SW-1 | | SW-3 | | PC's |
 +------+ +------+ +------+
```

<img width="940" height="558" alt="image" src="https://github.com/user-attachments/assets/1026dfb7-b4f2-4a58-bd28-2623cb5a752c" />

## Pembagian VLAN

| VLAN ID | Nama VLAN | IP Network       | DHCP Server | Switch yang Mengandung VLAN |
|---------|-----------|------------------|-------------|----------------------------|
| 10      | Marketing | 10.10.10.0/24   | Ya          | SW-1, SW-3, SW-4           |
| 20      | Sales     | 20.20.20.0/24   | Ya          | SW-1, SW-3, SW-4           |
| 30      | Finance   | 30.30.30.0/24   | Ya          | SW-1, SW-3, SW-4           |

## Konfigurasi Router

```
R1#show running-config

Building configuration...

Current configuration : 1234 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
ip dhcp pool marketing
   network 10.10.10.0 255.255.255.0
   default-router 10.10.10.1
   dns-server 10.10.10.1
!
ip dhcp pool sales
   network 20.20.20.0 255.255.255.0
   default-router 20.20.20.1
   dns-server 20.20.20.1
!
ip dhcp pool finance
   network 30.30.30.0 255.255.255.0
   default-router 30.30.30.1
   dns-server 30.30.30.1
!
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.10.10.1 255.255.255.0
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 20.20.20.1 255.255.255.0
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 30.30.30.1 255.255.255.0
!
ip classless
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
end
```

## Konfigurasi Switch

### SW-1

```
SW-1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3
10   marketing                        active    Fa0/4, Fa0/5, Fa0/6, Fa0/7, Fa0/8, Fa0/9, Fa0/10
20   sales                            active    Fa0/11, Fa0/12, Fa0/13, Fa0/14, Fa0/15, Fa0/16, Fa0/17
30   finance                          active    Fa0/18, Fa0/19, Fa0/20, Fa0/21, Fa0/22, Fa0/23, Fa0/24
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

### SW-3

```
SW-3#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3
10   marketing                        active    Fa0/4, Fa0/5, Fa0/6, Fa0/7, Fa0/8, Fa0/9, Fa0/10
20   sales                            active    Fa0/11, Fa0/12, Fa0/13, Fa0/14, Fa0/15, Fa0/16, Fa0/17
30   finance                          active    Fa0/18, Fa0/19, Fa0/20, Fa0/21, Fa0/22, Fa0/23, Fa0/24
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

### SW-4

```
SW-4#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
10   marketing                        active    Fa0/4, Fa0/5, Fa0/6, Fa0/7, Fa0/8, Fa0/9, Fa0/10
20   sales                            active    Fa0/11, Fa0/12, Fa0/13, Fa0/14, Fa0/15, Fa0/16, Fa0/17
30   finance                          active    Fa0/18, Fa0/19, Fa0/20, Fa0/21, Fa0/22, Fa0/23, Fa0/24
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

```
SW-4#show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1
Fa0/2       on           802.1q         trunking      1
Fa0/3       on           802.1q         trunking      1

Port        Vlans allowed on trunk
Fa0/1       1-1005
Fa0/2       1-1005
Fa0/3       1-1005

Port        Vlans allowed and active in management domain
Fa0/1       1,10,20,30
Fa0/2       1,10,20,30
Fa0/3       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,20,30
Fa0/2       1,10,20,30
Fa0/3       1,10,20,30
```

## Hasil Testing

### Testing DHCP

| VLAN     | Device       | Hasil IP          | Gateway          | DNS Server       | Status |
|----------|--------------|-------------------|------------------|------------------|--------|
| VLAN 10  | PC Marketing | 10.10.10.2       | 10.10.10.1       | 10.10.10.1       | Success|
| VLAN 20  | PC Sales     | 20.20.20.2       | 20.20.20.1       | 20.20.20.1       | Success|
| VLAN 30  | PC Finance   | 30.30.30.2       | 30.30.30.1       | 30.30.30.1       | Success|

### Testing Antar VLAN yang Sama

| Dari VLAN | Ke VLAN | Dari Device       | Ke Device         | Hasil |
|-----------|---------|-------------------|-------------------|-------|
| 10        | 10      | PC Marketing SW-1 | PC Marketing SW-3 | Success |
| 10        | 10      | PC Marketing SW-3 | PC Marketing SW-4 | Success |
| 20        | 20      | PC Sales SW-1     | PC Sales SW-3     | Success |
| 20        | 20      | PC Sales SW-3     | PC Sales SW-4     | Success |
| 30        | 30      | PC Finance SW-1   | PC Finance SW-3   | Success |
| 30        | 30      | PC Finance SW-3   | PC Finance SW-4   | Success |

### Testing Antar VLAN yang Berbeda

| Dari VLAN | Ke VLAN | Dari Device       | Ke Device         | Hasil |
|-----------|---------|-------------------|-------------------|-------|
| 10        | 20      | PC Marketing SW-1 | PC Sales SW-1     | Failed |
| 10        | 30      | PC Marketing SW-1 | PC Finance SW-1   | Failed |
| 20        | 30      | PC Sales SW-1     | PC Finance SW-1   | Failed |

### Testing ke Gateway

| VLAN     | Device       | Gateway          | Hasil |
|----------|--------------|------------------|-------|
| VLAN 10  | PC Marketing | 10.10.10.1       | Success |
| VLAN 20  | PC Sales     | 20.20.20.1       | Success |
| VLAN 30  | PC Finance   | 30.30.30.1       | Success |

## Kesimpulan

Dari hasil pengujian, dapat disimpulkan bahwa:
1. Konfigurasi VLAN telah berhasil diimplementasikan pada semua switch.
2. Konfigurasi DHCP server pada router berfungsi dengan baik dan memberikan IP address secara otomatis kepada setiap device.
3. Perangkat dalam satu VLAN yang sama dapat saling berkomunikasi meskipun berada pada switch yang berbeda.
4. Perangkat dalam VLAN yang berbeda tidak dapat saling berkomunikasi sesuai dengan konsep VLAN.
5. Semua perangkat dapat terhubung ke gateway masing-masing VLAN dengan baik.

---
**luqmanaru**
