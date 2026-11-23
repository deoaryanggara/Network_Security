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
