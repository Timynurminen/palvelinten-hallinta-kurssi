# h2-Voileipä
## x) Lue ja tiivistä
### Karvinen 2026: Sudo without password

- Salasanaton sudo mahdollistaa komentojen ajamisen root-oikeuksilla ilman salasanan syöttöä
- Luodaan käyttäjä ja lisätään se ryhmään (esim. sudoless)
- Sudoers-tiedostoon lisätään sääntö:
  ```bash
  %sudoless aLL = (ALL) NOPASSWD: ALL
  ```
- visudo-komennolla muokataan sudo-asetuksia turvallisesti
- Muutokset vaativat uudelleenkirjautumisen ennen kuin toimivat
- Toiminta testataan komennolla sudo, ilman että salasanaa kysytään
  #### Oma kysymys:
  - Mikä olisi turvallisempi vaihtoehto kuin antaa oikeus kaikkiin komentoihin (ALL)?
### Munroe 2006: xkcd 149: Sandwich

- Sarjakuvassa henkilö pyytää toista tekemään voileivän ja se ei onnistu
- Kun sama käsky annetaan muodossa "sudo make me a sandwich", se onnistuu
- Vitsi perustuu siihen, että sudo antaa käyttäjälle korkeat oikeudet
- Havainnollistaa, miten suuri merkitys käyttöoikeuksilla on tietokoneissa
  #### Oma huomio:
  - Sudo antaa paljon valtaa, joten väärinkäytettynä se voi olla myös turvallisuusriski
### Karvinen 2026: Passwordless Sudo with Ansible

- Ansible-rooli voi automatisoida käyttäjän SSH-avaimen ja salasanattoman sudon
- Luodaan sudoless-ryhmä ja lisätään käyttäjä siihen
- Lisätään sudoers-sääntö tiedostoon /etc/sudoers-d/
  ```bash
  %sudoless ALL = (ALL) NOPASSWD: ALL
  ```
- SSH-avain lisätään authorized_key-moduulilla automaattista kirjautumista varten
- Ensimmäisellä ajolla tarvitaan salasana (--ask-become-password)
- IaC- periaate: tee ensin manuaalisesti, sitten automatisoi
  #### Oma kysymys:
  - Voisiko sudo-oikeudet rajata vain tiettyihin komentoihin turvallisuuden parantamiseksi?
### Ansible-doc
- copy
- - Moduuli kopioi tiedostoja tai kirjoittaa sisältö kohdekoneelle
  - content: tiedoston sisältö suoraan
  - src: lähdetiedosto
  - dest: kohdepolku
  - owner/group/mode: omistaja, ryhmä ja oikeudet
  - Esimerkki:
    ```bash
    -copy:
        content: "Hei maailma\n"
        dest: /tmp/testi.txt
    ```
- apt
- - Moduuli hallitsee Debian/Ubuntu-paketteja
  - name: paketin nimi
  - state: present (asennettu) / absent (poistettu)
  - update_cache: päivittää pakettilistan
  - Esimerkki:
    ```bash
    -apt:
      name: nginx
      state: presenet
      update_cache: yes
    ```
- file
- - Moduuli hallitsee tiedostoja ja hakemistoja
  - path: kohteen polku
  - state: file / directory
  - recurse: koskee myös alihakemistoja
  - owner/group/mode: oikeudet ja omistajuus
  - Esimerkki
  ```bash
  - file:
      path: /tmp/kansio
      state: directory
  ```
- user
- - Moduuli hallitsee käyttäjiä
  - name: käyttäjän nimi
  - create_home: luo kotihakemisto
  - comment: kuvaus
  - groups: ryhmät
  - shell: komentotulkki
  - state: present / absent
  - system: järjestelmäkäyttäjä
  - Esimerkki
  ```bash
  - user:
      name: antero
      create_home: yes
      shell: /bin/bash
  ```
- authorized key
- - Moduuli lisää SSH-avaimen käyttäjälle
  - user: käyttäjä
  - key: julkinen SSH-avain 
      

