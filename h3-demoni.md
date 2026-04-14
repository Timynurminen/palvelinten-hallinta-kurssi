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

