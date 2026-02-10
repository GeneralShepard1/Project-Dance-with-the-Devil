# Koraci za IS (Hyper-V + Kali + ServerIspit) — Cheat Sheet

Ovaj README pokriva kompletne korake: konfiguracija VM-ova, prava pristupa (chmod), DNS/MX provjera za `unmo.ba`, skeniranje targeta, provjera CVE ranjivosti i osnovne pomoćne komande.

---

## 1) Konfiguracija virtualnih mašina (Hyper-V)

1. Pokrenuti **Hyper-V Manager**  
2. Kreirati **Private Switch** kroz **Virtual Switch Manager**  
3. Pokrenuti i konfigurisati 2 VM:

### a) VM Kali
- Mašina je već povezana i spremna
- Potrebno je samo bootati je

### b) VM ServerIspit
- Generacija: **1**
- CPU: **1**
- RAM: **2048 MB**
- Disk: nalazi se u folderu `C:\Install`
- Network: **Private Switch**

---

## 2) Zadatak: Direktorij sa brojem indeksa + privilegije

Na Kali VM:

1. Kreirati direktorij sa brojem indeksa  
2. Podesiti privilegije tako da **samo vi (user)** imate pristup  
3. Uraditi listing da se vide privilegije

### Komande

```bash
mkdir BrIndeksa
chmod 700 BrIndeksa
ls -la
```

### Objašnjenje chmod (brzo)

- `700` → sve privilegije za usera (rwx), niko drugi nema ništa  
- `770` → sve privilegije za usera i group, others nema ništa  
- `777` → svi imaju sve (najnesigurnije)

Napomena (ispravno tumačenje):
- `770` = user + **group** imaju sve (ne "others")
- `777` = user + group + others imaju sve

---

## 3) Zadatak: Odrediti mail server za www.unmo.ba

Ideja:
1. `dig MX` da nađeš mail server domene  
2. `ping` mail host da dobiješ IP adresu (ili `dig A` / `dig +short`)

### Primjer (iz lab-a)

- `dig unmo.ba mx` → rezultat: `edge2019.unmo.ba`
- `ping edge2019.unmo.ba` → IP: `195.130.50.20`

### Komande

```bash
dig unmo.ba MX
ping edge2019.unmo.ba
```

---

## 4) Zadatak: Skeniranje servera (servisi + verzije + OS)

Sa Kali VM skenirati server koji je konfigurisan u koraku 1 i utvrditi:
- koji su servisi aktivni
- koje su verzije servisa
- identifikaciju OS-a

### Komanda

```bash
nmap -sV -O 172.31.192.5
```

Napomena:
- Nakon što vidiš verzije servisa, tražiš poznate ranjivosti za te verzije i bilježiš.

---

## 5) Zadatak: Provjera ranjivosti (CVE)

Provjeriti da li je server ranjiv na:

- CVE-2021-44228 (Log4Shell)  
  https://github.com/r00thunter/Log4Shell
- CVE-2021-41773  
  https://github.com/lorddemon/CVE-2021-41773-PoC
- CVE-2021-42013  
  https://github.com/walnutsecurity/cve-2021-42013

### Koraci

1. Klonirati skenere sa GitHub-a  
2. Prebaciti Kali na **Private Switch**  
3. Deaktivirati NetworkManager  
4. Obrisati staru IP adresu i setovati novu u istom subnetu kao server  
   - Server: `172.31.192.5/20`
   - Kali primjer: `172.31.192.4/20`
5. Ping test (mora raditi)  
6. Ući u direktorij skenera  
7. Pokrenuti skener po uputstvu repo-a

### Komande (template)

```bash
git clone <URL>

sudo systemctl stop NetworkManager
sudo systemctl disable NetworkManager

sudo ip addr flush dev eth0
sudo ip addr add 172.31.192.4/20 dev eth0
sudo ip route add default via 172.31.192.1

ping 172.31.192.5

cd <DIRECTORY_NAME>
python3 <SCRIPT_NAME>.py 172.31.192.5
```

Napomena:
- Gateway `via 172.31.192.1` je primjer; koristi onaj koji važi u tvojoj mreži ako je drugačije.

---

## 6) Zadatak: Objasniti kako riješiti ranjivost pronađenu u zadatku 5

Primjeri mitigacije (zavisno od CVE):

- **Log4Shell (CVE-2021-44228)**:
  - update Log4j na sigurnu verziju
  - privremene mitigacije: disable JNDI lookup, setovanje preporučenih JVM/system varijabli
  - ograničiti egress/LDAP/RMI, WAF pravila

- **Apache path traversal / RCE (CVE-2021-41773 / CVE-2021-42013)**:
  - update Apache (2.4.51+ / 2.4.52+ prema preporukama)
  - isključiti ranjive module/config
  - pravilno konfigurisan `Require all denied` za osjetljive lokacije

---

## Dodatne komande (permissions, DNS, SMTP provjera)

### chmod primjeri

```bash
mkdir XXX
chmod 755 XXX              # Read/Execute za sve, Write samo vlasnik
chmod 750 XXX              # Read/Execute za grupu, ništa za ostale
chmod 644 XXX/datoteka     # Datoteka: read za sve, write samo vlasnik
```

### DNS komande

```bash
dig MX www.unmo.ba       # Mail server
dig A www.unmo.ba        # IP adresa
dig NS www.unmo.ba       # Nameserveri
dig SOA www.unmo.ba      # Authority / SOA

whois www.unmo.ba        # Registrar informacije
dig www.unmo.ba          # DNS records
nslookup www.unmo.ba     # Dodatna verifikacija
```

### SMTP test (ako nađeš mail server)

```bash
nslookup mail.unmo.ba
telnet <IP> 25
# ili
nc -v mail.unmo.ba 25
```

---

## AI alati (quick list)

- https://www.perplexity.ai/
- https://chat.deepseek.com/
- https://grok.com/
- https://chat.akash.network/
- https://notegpt.io/ai-chat
- https://chat.qwen.ai/

---

## Metasploit (osnovne komande)

```bash
msfconsole

search apache
search type:exploit smb
search platform:windows smb
search type:exploit platform:windows smb
search type:exploit apache
search type:exploit apache 2.4
```
