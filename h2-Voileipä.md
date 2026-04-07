# h2-Voileipä
## x) Lue ja tiivistä
### Karvinen 2026: Sudo without password

- Salasanaton sudo mahdollistaa komentojen ajamisen root-oikeuksilla ilman salasanan syöttöä
- Luodaan käyttäjä ja lisätään se ryhmään (esim. sudoless)
- Sudoers-tiedostoon lisätään sääntö:
  ```bash
  %sudoless ALL = (ALL) NOPASSWD: ALL
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
- Lisätään sudoers-sääntö tiedostoon /etc/sudoers.d/
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
  - Moduuli kopioi tiedostoja tai kirjoittaa sisältö kohdekoneelle
  - content: tiedoston sisältö suoraan
  - src: lähdetiedosto
  - dest: kohdepolku
  - owner/group/mode: omistaja, ryhmä ja oikeudet
  - Esimerkki:
    ```bash
    - copy:
        content: "Hei maailma\n"
        dest: /tmp/testi.txt
    ```
- apt
  - Moduuli hallitsee Debian/Ubuntu-paketteja
  - name: paketin nimi
  - state: present (asennettu) / absent (poistettu)
  - update_cache: päivittää pakettilistan
  - Esimerkki:
    ```bash
    -apt:
      name: nginx
      state: present
      update_cache: yes
    ```
- file
  - Moduuli hallitsee tiedostoja ja hakemistoja
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
  - Moduuli hallitsee käyttäjiä
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
- authorized_key
  - Moduuli lisää SSH-avaimen käyttäjälle
  - user: käyttäjä
  - key: julkinen SSH-avain 
      
## a) Sudoless

Tässä tehtävässä tein Ansiblella käyttäjän timster, jolle automatisoin
- SSH- kirjautumisen julkisella avaimella
- sudo-oikeuden ilman salasanaa

Olin tehnyt asetukset ensin käsin ja tämän jälkeen automatisoin ne Ansiblella (manual before auto)

### Projektin rakenne

 <img width="417" height="266" alt="image" src="https://github.com/user-attachments/assets/88da6ea4-fcb1-4c33-a597-ad261c73af5b" />


### Playbookin ajo
```bash
ansible-playbook site.yml
```
 <img width="1197" height="200" alt="image" src="https://github.com/user-attachments/assets/79585458-8c4d-487a-9c6b-a575b1ca83e3" />

```bash
ansible-playbook site.yml -K
```
 <img width="1201" height="524" alt="image" src="https://github.com/user-attachments/assets/ed455bdd-1f06-46b6-ac1a-aeb460ecf3eb" />
 
Toisella ajokerralla tuli:
- changed=4: Ansible teki muutoksia

### Sudo toimii ilman salasanaa
Kirjauduin käyttäjälle timster ja testasin:

<img width="1018" height="265" alt="image" src="https://github.com/user-attachments/assets/174d7ddf-3a79-4766-ad0d-579e57d89f46" />

### Sudoers-tiedosto

 <img width="548" height="35" alt="image" src="https://github.com/user-attachments/assets/0f49129e-05d9-4fa3-8fbd-9972fa20a4aa" />

## b) Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella

Käyttäjä timster luotiin jo kohdassa a), jossa automatisoin myös SSH-avaimen lisäämisen Ansiblella.

SSH-kirjautuminen ilman salasanaa toteutettiin seuraavalla tehtävällä:

<img width="1199" height="71" alt="image" src="https://github.com/user-attachments/assets/c906ac35-23fe-4a1b-a7d6-aec098336b53" />

Testasin kirjautumisen:

<img width="1069" height="213" alt="image" src="https://github.com/user-attachments/assets/82144cdd-445f-4cb6-bbb6-ad432922bcaa" />

Kirjautuminen onnistui ilman salasanaa

## c) Package. Asenna kaksi pakettia Ansiblella.

#### Tässä tehtävässä asensin kaksi pakettia Ansiblella käyttäen apt-moduulia
Paketit:
- htop
- tree

#### site.yml:

<img width="248" height="181" alt="image" src="https://github.com/user-attachments/assets/637f80c3-2a1f-4228-8ad3-0b49c6db63b9" />

#### roles/package/tasks/main.yml

<img width="238" height="127" alt="image" src="https://github.com/user-attachments/assets/6031ec41-5f45-4f0e-a6f9-ff1988ebb85d" />

#### playbookin ajo:

<img width="1181" height="539" alt="image" src="https://github.com/user-attachments/assets/34cf6a1c-3c18-482a-997b-0f564ec8a8cd" />

#### tree package testaus sekä samalla tree:n näyttö:

<img width="384" height="318" alt="image" src="https://github.com/user-attachments/assets/e5944f78-5573-448e-9393-041187874505" />

#### htop testaus:

<img width="1265" height="716" alt="image" src="https://github.com/user-attachments/assets/14471168-27ba-447a-8119-045e328c6188" />

#### Huomio
Paketit eivät välttämättä asennu oikein, jos pakettilista ei ole ajan tasalla. 

Ratkaisin tämän lisäämällä:

```bash
update_cache: yes
```

## d) File.

Tässä tehtävässä loin Ansiblella useamman rivin sisältävän tiedoston ja määrittelin sille omistajan, ryhmän ja oikeudet.

#### Playbook roles/file/tasks/main.yml

Sisältö:

<img width="542" height="178" alt="image" src="https://github.com/user-attachments/assets/d80c8071-0e62-466c-b08d-167ec346725b" />

#### site.yml päivitys

<img width="165" height="152" alt="image" src="https://github.com/user-attachments/assets/21a83d65-05dd-450e-986c-fd4da24b8057" />

#### Playbookin ajo

<img width="1166" height="606" alt="image" src="https://github.com/user-attachments/assets/da7ab2e8-4ab0-4e4a-a059-f54912d17f1d" />

#### Testaus


```bash
sudo micro /tmp/ansible-tiedosto.txt
```

<img width="349" height="70" alt="image" src="https://github.com/user-attachments/assets/8eb9dce8-6708-411e-b2f7-4f04952c011c" />

```bash
sudo cat /tmp/ansible-tiedosto.txt
```

<img width="678" height="79" alt="image" src="https://github.com/user-attachments/assets/e314ff5c-3640-41ac-afc3-41508eea3f87" />

### Oikeudet

<img width="711" height="43" alt="image" src="https://github.com/user-attachments/assets/835319f5-5f29-4fdd-a399-92272a1242f7" />

Oktaalimuodossa:
- 0600

Symbolisessa muodossa:

```bash
-rw-------
```

#### Mitä oikeudet tarkoittavat
- Omistaja (timster):
  - Saa lukea (r)
  - Saa kirjoittaa (w)
- Ryhmä:
  - ei oikeuksia
- Muut käyttäjät:
  - ei oikeuksia

Vain omistaja voi käyttää tiedostoa

## e) jotain muuta.

Tehtävässä käytin Ansiblen ping-moduulia, jolla voidaan testata että yhteys kohdekoneeseen toimii.

#### Komento

```bash
ansible all -m ping
```

#### Testaus

<img width="536" height="97" alt="image" src="https://github.com/user-attachments/assets/d85c67ad-0de5-4e72-9e7a-a0da305bddb5" />

#### Miksi?

- ping-moduulilla voi testata nopeasti, että Ansible-yhteys toimii
- pong-vastaus kertoo, että yhteys ja perusasetukset ovat kunnossa
- hyödyllinen nopea testi ennen playbookkien ajoa.


## Lähteet

- Karvinen 2026: [Sudo without password](https://terokarvinen.com/passwordless-sudo/)
- Karvinen 2026: [Passwordless Sudo with Ansible](https://terokarvinen.com/passwordless-sudo-with-ansible/)
- Ansible Community Documentation 2026: [docs.ansible](https://docs.ansible.com/projects/ansible/latest/collections/ansible/index.html)

