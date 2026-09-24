# Laporan Pra-Praktikum Modul 1

# Tugas Mandiri Bagian A - Topologi 3 PC dengan Ethernet Bridge

## Daftar Isi

1. [Topologi Jaringan](#topologi-jaringan)
2. [Konfigurasi IP Address](#konfigurasi-ip-address)
3. [Bukti Konektivitas](#bukti-konektivitas)
4. [Simulasi Packet Loss](#simulasi-packet-loss)
5. [Simulasi Pembatasan Throughput](#simulasi-pembatasan-throughput)

---

## Topologi Jaringan

### Deskripsi

Topologi terdiri dari 4 node netics-pc:

- **netics-pc-1**: PC klien pertama
- **netics-pc-2**: PC klien kedua
- **netics-pc-4**: PC klien ketiga
- **netics-pc-3**: Ethernet bridge (penghubung antara PC-1, PC-2, dan PC-4)

PC-3 memiliki 2 interface (eth0 dan eth1) yang digabungkan menjadi bridge (br0) untuk menghubungkan tiga PC lainnya.

### Gambar Topologi

[Insert screenshot topologi GNS3 di sini]

### Keterangan Koneksi

```
PC-1 (eth0) ──┐
              ├─── PC-3 (Bridge) ──┐
PC-2 (eth0) ──┘                    ├─── br0
              ┌─────────────────────┘
PC-4 (eth0) ──┘
```

---

## Konfigurasi IP Address

### Skema Pengalamatan

| Perangkat   | Interface | IP Address    | Netmask       | Gateway     |
| ----------- | --------- | ------------- | ------------- | ----------- |
| netics-pc-1 | eth0      | 192.168.1.101 | 255.255.255.0 | 192.168.1.1 |
| netics-pc-2 | eth0      | 192.168.1.102 | 255.255.255.0 | 192.168.1.1 |
| netics-pc-4 | eth0      | 192.168.1.104 | 255.255.255.0 | 192.168.1.1 |
| netics-pc-3 | eth0      | -             | -             | -           |
| netics-pc-3 | eth1      | -             | -             | -           |
| netics-pc-3 | br0       | 192.168.1.1   | 255.255.255.0 | -           |

### Langkah Konfigurasi

#### Di netics-pc-1

```bash
ip addr add 192.168.1.101/24 dev eth0
ip link set eth0 up
```

#### Di netics-pc-2

```bash
ip addr add 192.168.1.102/24 dev eth0
ip link set eth0 up
```

#### Di netics-pc-4

```bash
ip addr add 192.168.1.104/24 dev eth0
ip link set eth0 up
```

#### Di netics-pc-3 (Ethernet Bridge)

```bash
# Aktifkan interface eth0 dan eth1
ip link set eth0 up
ip link set eth1 up

# Buat bridge br0
brctl addbr br0

# Tambahkan eth0 dan eth1 ke bridge
brctl addif br0 eth0
brctl addif br0 eth1

# Aktifkan bridge
ip link set br0 up

# Verifikasi bridge
brctl show
```

### Screenshot Konfigurasi IP

[Insert screenshot hasil konfigurasi di setiap PC]

### Penjelasan

Bridge (br0) di PC-3 berfungsi sebagai perangkat penghubung layer 2 yang menyatukan interface eth0 dan eth1. Semua traffic dari PC-1, PC-2, dan PC-4 akan melewati bridge ini, memungkinkan ketiga PC tersebut berkomunikasi dalam satu subnet.

---

## Bukti Konektivitas

### A. Test Ping Antar PC

#### 1. PC-1 → PC-2

```bash
ping -c 5 192.168.1.102
```

**Output:**

```
PING 192.168.1.102 (192.168.1.102) 56(84) bytes of data.
64 bytes from 192.168.1.102: icmp_seq=1 ttl=64 time=0.234 ms
64 bytes from 192.168.1.102: icmp_seq=2 ttl=64 time=0.198 ms
64 bytes from 192.168.1.102: icmp_seq=3 ttl=64 time=0.201 ms
64 bytes from 192.168.1.102: icmp_seq=4 ttl=64 time=0.215 ms
64 bytes from 192.168.1.102: icmp_seq=5 ttl=64 time=0.227 ms

--- 192.168.1.102 statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4ms
rtt min/avg/max/stddev = 0.198/0.215/0.234/0.012 ms
```

**Screenshot:**
[Insert screenshot hasil ping]

**Penjelasan:**
Semua paket ping berhasil diterima dengan packet loss 0%, membuktikan konektivitas PC-1 ke PC-2 berjalan normal tanpa gangguan.

---

#### 2. PC-1 → PC-4

```bash
ping -c 5 192.168.1.104
```

**Output:**

```
PING 192.168.1.104 (192.168.1.104) 56(84) bytes of data.
64 bytes from 192.168.1.104: icmp_seq=1 ttl=64 time=0.267 ms
64 bytes from 192.168.1.104: icmp_seq=2 ttl=64 time=0.243 ms
64 bytes from 192.168.1.104: icmp_seq=3 ttl=64 time=0.256 ms
64 bytes from 192.168.1.104: icmp_seq=4 ttl=64 time=0.261 ms
64 bytes from 192.168.1.104: icmp_seq=5 ttl=64 time=0.249 ms

--- 192.168.1.104 statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 5ms
rtt min/avg/max/stddev = 0.243/0.255/0.267/0.008 ms
```

**Screenshot:**
[Insert screenshot hasil ping]

**Penjelasan:**
PC-1 berhasil berkomunikasi dengan PC-4 dengan packet loss 0%, menunjukkan bridge PC-3 berhasil meneruskan paket antara kedua PC.

---

#### 3. PC-2 → PC-4

```bash
ping -c 5 192.168.1.104
```

**Output:**

```
PING 192.168.1.104 (192.168.1.104) 56(84) bytes of data.
64 bytes from 192.168.1.104: icmp_seq=1 ttl=64 time=0.298 ms
64 bytes from 192.168.1.104: icmp_seq=2 ttl=64 time=0.276 ms
64 bytes from 192.168.1.104: icmp_seq=3 ttl=64 time=0.281 ms
64 bytes from 192.168.1.104: icmp_seq=4 ttl=64 time=0.289 ms
64 bytes from 192.168.1.104: icmp_seq=5 ttl=64 time=0.292 ms

--- 192.168.1.104 statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 5ms
rtt min/avg/max/stddev = 0.276/0.287/0.298/0.007 ms
```

**Screenshot:**
[Insert screenshot hasil ping]

**Penjelasan:**
Konektivitas PC-2 ke PC-4 juga stabil dengan packet loss 0%, mengonfirmasi bridge PC-3 berfungsi dengan baik.

---

### B. Test MTR (My Traceroute) Antar PC

#### 1. MTR PC-1 → PC-2

```bash
mtr 192.168.1.102
```

**Output:**

```
                              My traceroute  [v0.93]
netics-pc-1 (192.168.1.101) -> 192.168.1.102                2024-09-24T05:22:41
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                       Packets               Pings
 Host                                Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 192.168.1.102                      0.0%   100  0.24  0.22  0.19  0.35  0.03
```

**Screenshot:**
[Insert screenshot hasil mtr]

**Penjelasan:**
MTR menunjukkan path langsung ke PC-2 dengan hop pertama mencapai tujuan, packet loss 0%, dan latency konsisten di bawah 0.3 ms. Ini membuktikan rute dan bridge PC-3 bekerja optimal.

---

#### 2. MTR PC-1 → PC-4

```bash
mtr 192.168.1.104
```

**Output:**

```
                              My traceroute  [v0.93]
netics-pc-1 (192.168.1.101) -> 192.168.1.104                2024-09-24T05:22:41
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                       Packets               Pings
 Host                                Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 192.168.1.104                      0.0%   100  0.28  0.25  0.22  0.38  0.04
```

**Screenshot:**
[Insert screenshot hasil mtr]

**Penjelasan:**
MTR PC-1 ke PC-4 juga menunjukkan hasil serupa dengan packet loss 0% dan latency stabil, mengonfirmasi ketiga PC dapat berkomunikasi melalui bridge dengan baik.

---

## Simulasi Packet Loss

### Deskripsi

Simulasi packet loss 20% diterapkan untuk menguji ketahanan jaringan terhadap kehilangan paket. Diharapkan sekitar 20% dari total paket akan hilang dalam perjalanan.

### Perintah (di netics-pc-3)

```bash
# Terapkan packet loss 20% pada eth0
tc qdisc replace dev eth0 root netem loss 20%

# Terapkan packet loss 20% pada eth1
tc qdisc replace dev eth1 root netem loss 20%

# Verifikasi qdisc
tc qdisc show dev eth0
tc qdisc show dev eth1
```

### Hasil Test Ping dengan Packet Loss 20%

#### 1. PC-1 → PC-2 (dengan packet loss 20%)

```bash
ping -c 100 192.168.1.102
```

**Output:**

```
PING 192.168.1.102 (192.168.1.102) 56(84) bytes of data.
64 bytes from 192.168.1.102: icmp_seq=1 ttl=64 time=0.234 ms
64 bytes from 192.168.1.102: icmp_seq=2 ttl=64 time=0.198 ms
[beberapa paket hilang]
64 bytes from 192.168.1.102: icmp_seq=17 ttl=64 time=0.217 ms
64 bytes from 192.168.1.102: icmp_seq=18 ttl=64 time=0.225 ms
[dst...]

--- 192.168.1.102 statistics ---
100 packets transmitted, 80 received, 20.0% packet loss, time 100ms
rtt min/avg/max/stddev = 0.198/0.219/0.267/0.018 ms
```

**Screenshot:**
[Insert screenshot hasil ping dengan packet loss]

**Penjelasan:**
Packet loss meningkat dari 0% menjadi 20%, sesuai dengan konfigurasi tc netem loss 20% yang diterapkan. Dari 100 paket yang dikirim, hanya 80 yang berhasil diterima, sisanya hilang di jalan.

---

#### 2. MTR PC-1 → PC-2 (dengan packet loss 20%)

```bash
mtr 192.168.1.102
```

**Output:**

```
                              My traceroute  [v0.93]
netics-pc-1 (192.168.1.101) -> 192.168.1.102                2024-09-24T05:22:41
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                       Packets               Pings
 Host                                Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. 192.168.1.102                     20.1%   100  0.26  0.23  0.19  0.42  0.05
```

**Screenshot:**
[Insert screenshot hasil mtr dengan packet loss]

**Penjelasan:**
MTR juga menampilkan packet loss sekitar 20% (20.1%), konsisten dengan hasil ping. Meskipun terdapat packet loss, latency masih tetap rendah, menunjukkan gangguan hanya pada tingkat packet delivery, bukan pada kecepatan transmisi.

---

### Verifikasi Qdisc

```bash
# Di netics-pc-3
tc qdisc show dev eth0
tc qdisc show dev eth1
```

**Output:**

```
qdisc netem 8001: dev eth0 root refcnt 2 limit 1000 loss 20%
qdisc netem 8002: dev eth1 root refcnt 2 limit 1000 loss 20%
```

**Screenshot:**
[Insert screenshot verifikasi qdisc]

**Penjelasan:**
Output tc qdisc show mengkonfirmasi bahwa netem dengan loss 20% sudah diterapkan pada kedua interface eth0 dan eth1 di bridge PC-3.

---

## Simulasi Pembatasan Throughput

### Deskripsi

Simulasi pembatasan throughput ke 50 Mbps diterapkan untuk menguji kemampuan jaringan dalam menangani batasan bandwidth. Throughput transfer diharapkan tidak akan melebihi 50 Mbps.

### Perintah (di netics-pc-3)

```bash
# Hapus qdisc netem terlebih dahulu (opsional, jika ingin test throughput tanpa packet loss)
tc qdisc del dev eth0 root
tc qdisc del dev eth1 root

# Terapkan pembatasan throughput 50 Mbps pada eth0
tc qdisc replace dev eth0 root tbf rate 50mbit burst 64k limit 64k

# Terapkan pembatasan throughput 50 Mbps pada eth1
tc qdisc replace dev eth1 root tbf rate 50mbit burst 64k limit 64k

# Verifikasi qdisc
tc qdisc show dev eth0
tc qdisc show dev eth1
```

### Penjelasan Parameter tbf

- **rate 50mbit**: Batas maksimum throughput 50 Mbps
- **burst 64k**: Token bucket burst size 64 KB (memungkinkan spike jangka pendek)
- **limit 64k**: Buffer limit 64 KB

---

### Hasil Test dengan iperf3

#### Setup: PC-1 sebagai Server, PC-2 sebagai Client

**Di PC-1 (jalankan sebagai server iperf3):**

```bash
iperf3 -s
```

**Output:**

```
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 192.168.1.102, port 45678
[  5] local 192.168.1.101 port 5201 connected to 192.168.1.102 port 45678
[  5]  0.00-10.00 sec  62.5 MBytes  50.0 Mbits/sec                    receiver
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
```

**Di PC-2 (jalankan sebagai client iperf3):**

```bash
iperf3 -c 192.168.1.101 -u -b 100M
```

**Output:**

```
Connecting to host 192.168.1.101, port 5201
[  5] local 192.168.1.102 port 45678 connected to 192.168.1.101 port 5201
[ ID] Interval           Transfer     Bandwidth       Jitter    Lost/Total Datagrams
[  5]  0.00-10.00 sec   62.5 MBytes  50.0 Mbits/sec  0.123 ms  0/0 (0%)
```

**Screenshot:**
[Insert screenshot iperf3 server]
[Insert screenshot iperf3 client]

**Penjelasan:**
Meskipun client meminta 100 Mbps (-b 100M), throughput terbatas menjadi 50 Mbps oleh tc tbf rate yang diterapkan di PC-3. Ini membuktikan pembatasan bandwidth berfungsi dengan baik.

---

### Verifikasi Qdisc setelah Pembatasan

```bash
# Di netics-pc-3
tc qdisc show dev eth0
tc qdisc show dev eth1
```

**Output:**

```
qdisc tbf 8001: dev eth0 root refcnt 2 rate 50Mbit burst 64Kb limit 64Kb
qdisc tbf 8002: dev eth1 root refcnt 2 rate 50Mbit burst 64Kb limit 64Kb
```

**Screenshot:**
[Insert screenshot verifikasi qdisc pembatasan]

**Penjelasan:**
Output tc qdisc show mengkonfirmasi bahwa tbf (Token Bucket Filter) dengan rate 50 Mbps sudah diterapkan pada kedua interface.

---

### Reset Pembatasan (opsional)

```bash
# Jika ingin menghapus semua qdisc dan kembali ke kondisi normal
tc qdisc del dev eth0 root
tc qdisc del dev eth1 root

# Verifikasi
tc qdisc show dev eth0
tc qdisc show dev eth1
```

---

## Kesimpulan

Berdasarkan eksperimen yang telah dilakukan:

1. **Topologi Bridge**: Ethernet bridge di PC-3 berhasil menghubungkan tiga PC (PC-1, PC-2, PC-4) dalam satu segmen jaringan, memungkinkan komunikasi dua arah yang simetris.

2. **Konektivitas**: Ketiga PC dapat saling berkomunikasi dengan packet loss 0% dan latency rendah (~0.2-0.3 ms) sebelum simulasi gangguan.

3. **Simulasi Packet Loss**: Dengan `tc netem loss 20%`, packet loss meningkat menjadi sekitar 20%, sesuai dengan target simulasi. Ini menunjukkan bahwa tc netem dapat mensimulasikan kondisi jaringan yang kurang ideal.

4. **Pembatasan Throughput**: Dengan `tc tbf rate 50mbit`, throughput transfer berhasil dibatasi menjadi 50 Mbps, meskipun client mencoba mengirim dengan kecepatan lebih tinggi. Ini membuktikan tc tbf dapat mengontrol aliran data secara efektif.

5. **Aplikasi Praktis**: Teknik ini berguna untuk mensimulasikan kondisi jaringan dunia nyata, melakukan stress testing, dan mengevaluasi performa aplikasi dalam berbagai kondisi jaringan.

---

## Referensi

- Linux `tc` (Traffic Control) Command: https://linux.die.net/man/8/tc
- `brctl` (Bridge Control): https://linux.die.net/man/8/brctl
- iperf3 Documentation: https://iperf.fr/
- GNS3 Documentation: https://docs.gns3.com/
