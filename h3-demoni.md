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

