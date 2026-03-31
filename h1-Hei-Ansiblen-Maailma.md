# h1 Hei Ansiblen Maailma
## x) lue ja tiivistä:
### SSH public key - Login without password:
- SSH on yleinen ja turvallinen tapa kirjautua palvelimille.
- SSH-palvelin asennetaan paketista ssh ja käynnistetään systemctl enable --now ssh.
- Yhteys voidaan testata komennolla ssh localhost, jolloin kirjaudutaan samaan koneeseen SSH:n kautta.
- **Oma kysymys:** Miten turvallsuus paranee, jos salasanaa ei enää käytetä kirjautumisessa?

### Hello Ansible
- Ansible on konfiguraationhallintatyökalu, jossa infrastruktuuri kuvataan koodina ja muutoksia tehdään vain tarvittaessa.
- Se toimii SSH:n yli ilman agenttia, mutta kohdekoneessa pitää olla SSH ja Python.
- Hostit määritellään inventaariossa ja playbook määrittää mitä rooleja ajetaan.
- Roolit sisältävät tehtäviä, kuten tiedoston luonti, ja lopputulos voidaan tarkistaa esimerkiksi SSH:lla.
- **Oma kysymys:** Miten Ansible varmistaa, että sama playbook ei tee turhia muutoksia joka ajokerralla?

## a) Asenna SSH-demoni ja testaa se kirjautumalla SSH:lla
### Asenna SSH
```bash
- sudo apt-get install ssh
- sudo systemctl enable --now ssh
```
### Tarkista palvelun tila 
```bash
- sudo systemctl status ssh
```
### Testaa SSH-yhteys
```bash
- ssh localhost
```
### Yhteyden onnistuttua poistu komennolla
- exit

## b) Pubkey. Automatisoi ssh-kirjautuminen julkisella avaimella
 ### Luo avainpari
 ```bash
- ssh-keygen
```
 ### Kopioi avain localhostille
 ```bash
- ssh-copy-id localhost
```
### Testaus, jossa kirjoitat vielä salasanan
```bash
- ssh localhost
```
### Jos ei kysynyt salasanaa uudelleen se onnistui
- exit

## c) Hei Ansible. Tee hei maailma ansiblella ja kokeile sitä SSH:n yli.
### Asenna Ansible
```bash
- sudo apt-get install ansible
```
### Luo Ansible-kansio ja määrittele hostit tiedostoon hosts.ini
```bash
- mkdir ansible
- cd ansible
```
- (sudo apt-get install micro) (jos ei ollut micro tekstieditoria)
```bash
- micro hosts.ini
```
### hosts.ini sisälle:
```bash
- localhost

- [all:vars]
- ansible_python_interpreter=/usr/bin/python3
```
### Lisäksi luodaan tiedosto ansible.cfg, jotta ei tarvitse antaa -i hosts.ini jokaisessa komennossa
#### ansible.cfg:
```bash
- [defaults]
- inventory = hosts.ini
```
### Tee playbook site.yml, joka käyttää roolia hello
```bash
- micro site.yml
- mkdir -p roles/hello/tasks
- micro roles/hello/tasks/main.yml
```
#### site.yml:
```bash
 -hosts: all
   roles:
   - hello
```
#### main.yml:
```bash
  - copy:
      dest: /tmp/hello-ansible
      content: "Hei maailma Ansiblella\n"
```
 ### Aja playbook
 ```bash
- ansible-playbook site.yml
- (changed=1)
```
### Lopputuloksen testaus:
```bash
- ssh localhost 'cat /tmp/hello-ansible'
- Hei maailma Ansiblella
```
## Lähteet
- Karvinen 2026: [SSH public key - Login without password](https://terokarvinen.com/ssh-public-key-login-without-password/)
- Karvinen 2026: [Hello Ansible](https://terokarvinen.com/hello-ansible/)
- ChatGPT (OpenAI), käytetty apuna komentojen ja virhetilanteiden selvittämiseen 
