Koraci za IS: 

1. dio 

1. Konfiguracija virtualnih mašina 

Pokrenite Hyper-V Manager. 

Kreirajte Private Switch kroz Virtual Switch Manager. 

Pokrenite i konfigurišite 2 virtualne mašine: 

a) VM Kali mašina 

Mašina je već povezana i spremna 

Potrebno je samo bootati je 

b) VM ServerIspit mašina 

Generacija: 1 

CPU: 1 

RAM: 2048 MB 

Disk: nalazi se u folderu C:\Install 

Network: Private Switch 

----------------------------------------------------------------------------------------------------------------- 

2. zadatak ->  

Na Kali VM: 

Kreirati direktorij sa brojem indeksa. 

Podesiti privilegije tako da samo vi kao user imate pristup direktoriju 

Nakon kreiranja direktorija, izvršiti listing direktorija tako da se vide privilegije. 

 

KOMANDE: 

mkdir BrIndeksa 

chmod 700/770/777 NazivDirektorija/fajla 

700 -> sve privilegije za usera 
770 -> sve privilegije za usera i others 
777 -> sve privilegije za usera, others i group 

----------------------------------------------------------------------------------------------------------------- 

3. zadatak  -> 

Za kompaniju www.unmo.ba odrediti adresu mail servera. 

 

Prvo se sa komandom: dig unmo.ba mx provjeri na kojoj domeni se nalazi. 

Rezultat komande: edge2019.unmo.ba. 

Zatim sa pingom te domene mozemo doci do IP adrese. 

Komanda: ping edge2019.unmo.ba, rezultat toga 195.130.50.20. 

KOMANDE: 

dig unmo.ba mx 

ping edge2019.unmo.ba 

 

Ink 3, ShapeInk 1, Shape----------------------------------------------------------------------------------------------------------------- 

4. zadatak->  

Skeniranje servera 

Sa Kali VM skenirati server koji je konfigurisan u prvom koraku i utvrditi: 

Koji su servisi aktivni 

Koje su verzije servisa 

Identifikaciju operativnog sistema 

 

Prvi korak je prebaciti se na privatni switch na kojem je i virutalna druga masina. 

Komandom nmap uradimo scan otvorenih servisa. 

Kada vidimo verziju servisa, nadjemo na internetu koja je njegova ranjivost i to zapišemo. 

KOMANDE: 

nmap -sV -O 172.31.192.5 

------------------------------------------------------------------------------------------------------------------ 5. zadatak->  

Provjera ranjivosti 

Provjeriti da li je server ranjiv na neku od sljedećih ranjivosti: 

CVE-2021-44228 (Log4Shell) 
https://github.com/r00thunter/Log4Shell 

CVE-2021-41773 
https://github.com/lorddemon/CVE-2021-41773-PoC 

CVE-2021-42013 
https://github.com/walnutsecurity/cve-2021-42013 

Neke od ovih ranjivosti moguće je detektovati i pomoću nmap-a, ukoliko se zna odgovarajući način. 

 

1. korak: Kloniramo skenere sa githuba. 

2. korak: Prebaciti kali masinu na privatni switch.  

3. korak: Deaktivirati NetworkManager. 

4. korak: Obrisemo staru IP adresu. Setujemo novu IP adresu koja mora biti u istom subnetu kao I adresa servera. Adresa servera je bila: 172.31.192.5/20. Znači mozemo setovati novu IP adresu: 172.31.192.4/20. I onda provjerimo jel mozemo pingati svoj server. Ukoliko ne radi nismo setup IP adresu kako treba. 

5. korak: Na svakom githubu provjeriti kako se upotrebljava taj skener i pratiti upute kako se koristi.  

6. korak: Promjenimo direktorij. 

7. korak: Upaliti skener i pustiti da skenira. 

 

KOMANDE: 

git clone URL 

sudo systemctl stop NetworkManager 

sudo ip addr flush dev eth0 

sudo ip addr add IP_ADRESS dev eth0 

ping 172.31.192.5 

cd DIRECTORY_NAME 

python SCRIPT_NAME IP_ADRESA 

 

 

----------------------------------------------------------------------------------------------------------------- 

6. zadatak ->  

Objasniti kako riješiti ranjivost koja je pronađena u zadatku 5. 

________________________________________________________________________________________________________________
mkdir XXX
chmod 755 XXX              # Read/Execute za sve, Write samo vlasnik
chmod 750 XXX              # Read/Execute za grupu, Ništa za ostale
chmod 644 XXX/datoteka     # Datoteke sa različitim pravima


dig MX www.unmo.ba       # Mail server
dig A www.unmo.ba        # IP adresa
dig NS www.unmo.ba       # Nameserveri
dig SOA www.unmo.ba      # Authority

whois www.unmo.ba        # Registrar informacije
dig www.unmo.ba          # DNS records
nslookup www.unmo.ba     # Dodatna verifikacija


nslookup mail.unmo.ba               # Pronađi IP
telnet [IP] 25                      # Testiraj SMTP
# ili
nc -v mail.unmo.ba 25               # Netcat test








