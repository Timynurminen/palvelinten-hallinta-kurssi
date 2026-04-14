# h3 - Demoni

## x) Lue ja tiivistä

### Karvinen 2026: Apache installed with Ansible - quick notes

- Apache voidaan asentaa ja konfiguroida automaattisesti Ansiblella
- Web-palvelimen asennus noudattaa **package-file-service**- mallia
   - package: asennetaan ohjelma
   - file: muokataan asetustiedostoja
   - service: käynnistetään/päivitetään palvelu
- Konfiguraatiomuutokset tulevat voimaan vasta, kun palvelu käynnistetään uudelleen
- Ansible käyttää **notify + handler** - mekanismia palvelun uudelleenkäynnistykseen
- roolirakenne jakautuu:
    - tasks: varsinainen automaatio
    - files: konfiguraatiotiedostot
    - handlers: palvelun restart
- Apache voidaan konfiguroida käyttämään käyttäjän omaa hakemistoa (esim. /home/user/publicsite)

#### oma kysymys
- Miksi Ansible-roolit jaetaan kansioihin (tasks, files, handlers) eikä kaikki yhteen tiedostoon

### Handlers: running operations on change

- Handlerit ovat tehtäviä, jotka suoritetaan vain silloin, kun jokin muutos tapahtuu
- Niitä käytetään esimerkiksi palvelun uudelleenkäynnistykseen konfiguraation muuttuessa
- Handler aktivoidaan notify- komennolla tavallisesta taskista
- Handlerit suoritetaan oletuksena vasta playbookin lopussa
- Sama handler ajetaan vain kerran, vaikka sitä kutsuttaisiin useita kertoja

### Notifying handlers

- Task voi kutsua yhtä tai useampaa handleria notify-avainsanalla
- Handlerit suoritetaan siinä järjestyksessä kuin ne on määritelty, ei notify-listan mukaan
- Jos useampi task kutsuu samaa handleria, se suoritetaan vain kerran
- Tämä vähentää turhia palvelun uudelleenkäynnistyksiä

#### oma kysymys

- Miksi on tärkeätä, että palvelua ei käynnistetä uudelleen jokaisen muutoksen jälkeen erikseen?

### Ansible-doc service

- service-moduulia hallitaan palveluita (käynnistys, pysäytys, uudelleenkäynnistys) etäkoneilla
- Tukee eri järjestelmiä (esim. systemd, SysV, OpenRC)
- Toimii yleisenä rajapintana eri palvelunhallintajärjestelmille
- Mahdollistaa saman playbookin käytön eri ympäristöissä

### Parametrit
- name
  - palvelun nimi (pakollinen)
- state
  - määrittää palvelun tilan
  - vaihtoehdot
    - started: käynnissä
    - stopped: pysäytetty
    - restarted: käynnistetään uudelleen
    - reloaded: ladataan uudelleen
- enabled
  - määrittää käynnistyykö palvelu automaattisesti bootissa
  - arvot: true / false

- Esimerkki

```yaml
- name: Start nginx service
  ansible.builtin.service:
    name: nginx
    state: started
    enabled: true
```
#### Oma kysymys:

- Mikä ero on restarted ja reloaded, ja milloin kumpaa pitäisi käyttää?

## a) Apassi. Asenna Apache 2 käsin.

Tässä tehtävässä asensin Apache2-webpalvelimen käsin ja tein etusivun, jota voi muokata tavallisena käyttäjänä ilman sudo-oikeuksia

### Apache asennus

```bash
sudo apt-get update
sudo apt-get install apache2
```
<img width="771" height="334" alt="image" src="https://github.com/user-attachments/assets/21bf7648-0f63-4ba9-98ba-14ffd1d98e9d" />

### Käynnistys ja status

```bash
sudo systemctl enable --now apache2
sudo systemctl status apache2
```
<img width="1071" height="135" alt="image" src="https://github.com/user-attachments/assets/5eda0f6b-6143-4efa-aa06-2411c3b5fd98" />

### Testaus selaimessa

Avasin selaimessa: 
```bash
firefox http://localhost
```
<img width="870" height="189" alt="image" src="https://github.com/user-attachments/assets/2635d8e6-f82f-4f7e-8751-0bb13ba99baa" />

### Oman sivun tekeminen käyttäjälle

Loin kansion käyttäjän kotihakemistoon:

```bash
mkdir -p ~/publicsite
micro ~/publicsite/index.html
```
Sisältö: 
```html
<h1> Apassi </h1>
<p> Toimii! </p>
```
<img width="311" height="90" alt="image" src="https://github.com/user-attachments/assets/cf4a8384-e1be-4419-87ec-26769c0967b4" />

### Oikeudet

```bash
chmod ugo+x /home/timyn
cmohd ugo+x /home/timyn/publicsite
chmod ugo+r /home/timyn/publicsite/index.html
```
<img width="710" height="63" alt="image" src="https://github.com/user-attachments/assets/cece0402-2dd6-443e-863d-af24865d333f" />
kuvassa pieni virhe, jonka korjasin komennolla:
```bash
chmod 644 /home/timyn/publicsite/index.html
```
<img width="535" height="20" alt="image" src="https://github.com/user-attachments/assets/0557d3e5-acbb-4791-9db9-f6dc9b049e61" />
<img width="515" height="22" alt="image" src="https://github.com/user-attachments/assets/154ec88e-0a34-4d79-b286-fe877ee0c9a9" />

### Apache konfiguraatio
```bash
sudo micro /etc/apache2/sites-available/000-default.conf
```
Muokkasin: 
```apache
Documentroot /home/timyn/publicsite
```
Lisäsin myös:
```apache
<Directory /home/timyn/publicsite>
      Require all granted
</Directory>
```
<img width="849" height="629" alt="image" src="https://github.com/user-attachments/assets/0eb30c7d-0efb-4f43-a1a2-fcc95626423d" />

### Apachen uudelleenkäynnistys

<img width="564" height="25" alt="image" src="https://github.com/user-attachments/assets/40980ed2-8016-406d-bf7c-2a184d28df32" />

### Lopputulos:
```bash
firefox http://localhost
```
<img width="1279" height="222" alt="image" src="https://github.com/user-attachments/assets/fc2937e0-4ea0-4b0a-b3b0-50db1763c191" />

## b) Moottorix. Asenna Nginx käsin.

Tässä tehtävässä asensin nginx-webpalvelimen käsin ja tein etusivun, jota voi muokata tavallisena käyttäjänä ilman sudo-oikeuksia.

- Apache2 ja Nginx eivät toimi samaan aikaan, koska käyttävät samaa porttia, sammutin Apachen
```bash
sudo systemctl stop apache2
sudo systemctl disable apache2
```
<img width="1086" height="160" alt="image" src="https://github.com/user-attachments/assets/0494e9fe-290e-4429-9ba1-2d8158a3f15a" />

### Nginx asennus

```bash
sudo apt-get update
sudo apt-get install nginx
```
<img width="759" height="440" alt="image" src="https://github.com/user-attachments/assets/c570b223-bd5f-48af-ae0d-b517557adffc" />

### Käynnistys ja status

```bash
sudo systemctl enable --now nginx
sudo systemctl status nginx
```
<img width="1061" height="134" alt="image" src="https://github.com/user-attachments/assets/fe2ae8c9-c3e0-42ed-99f8-474278ed120a" />

### Testaus selaimessa

```bash
firefox http://localhost
```
Näytti Apachen default pagea, vaikka Apache oli pysäytetty.
Joten jouduin ensin poistamaan tiedoston
```bash
sudo rm /var/www/html/index.html
```
Tämän jälkeen näytti vieläkin apache default pagea, joten viimeisenä vielä poistin selaimen cachen kokonaan.
##### Lopputulos:

<img width="937" height="335" alt="image" src="https://github.com/user-attachments/assets/5551c84a-55f5-4aba-ac67-abaa29185cbf" />

### Oman sivun tekeminen

```bash
mkdir -p home/timyn/publicsite
micro /home/timyn/publicsite/index.html
```
Sisältö: 
```html
<h1> Moottorix </h1>
<p> Nginx Toimii Mainiosti! </p>
```
<img width="638" height="60" alt="image" src="https://github.com/user-attachments/assets/c4fb7d2f-dece-4234-8cae-cb5df92bf2e5" />

### Oikeudet

```bash
chmod ugo+x /home/timyn
chmod ugo+x /home/timyn/publicsite
chmod o+x /home/timyn/publicsite/index.html
```
<img width="765" height="103" alt="image" src="https://github.com/user-attachments/assets/639ca489-06b6-4937-80ac-544a6f3e0052" />

### Nginx konfiguraatio

```bash
sudo micro /etc/nginx/sites-available/default
```
Muokkasin:
```nginx
root /home/timyn/publicsite;
index index.html;
```
<img width="621" height="120" alt="image" src="https://github.com/user-attachments/assets/ce3bd3ba-04ae-4505-9acf-4a5cb8bf6fff" />

### Konfiguraation testaus

```bash
sudo nginx -t
```
<img width="674" height="57" alt="image" src="https://github.com/user-attachments/assets/e98e9003-2a7c-4dab-a82b-86d3ae920fcb" />

### Nginx uudelleen käynnistys

```bash
sudo systemctl restart nginx
```
### Lopputulos:

```bash
firefox http://localhost
```
<img width="391" height="196" alt="image" src="https://github.com/user-attachments/assets/c6b34403-ca95-4448-adfa-4c49fe7fede3" />

