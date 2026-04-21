# h4 Pizza fantasia

## Lue ja tiivistä x)

### 4.12.1 Size and Complexity of Some DSLs

- DSL-kielet voivat olla erittäin suuria ja monimutkaisia
- Esim. Salt DSL sisältää 510 funktiota ja dokumentaatio on erittäin laaja
- Kontrollirakenteet (if,for) tehdään erikseen esim. Jinja2-malleilla
- Tämä lisää abstraktitasoa ja tekee ymmärtämisestä vaikeampaa
- Puppetissa on vähemmän funktioita (113), mutta käyttää omia konsepteja (esim. "virtual resources")

### 4.12.2 Use of DSL Functions in Case Configuration

- Vaikka DSL:ssä on paljon funktioita, käytännössä käytetään vain pientä osaa
- Yleisimmät mallit
  - package (asennus)
  - file (tiedostot)
  - service (palvelut)
- Tämä muodostaa **package-file-service** -patternin
- Suurin osa käytöstä keskittyy muutamaan yleiseen funktioon (~87 %)
- Harvinaisia funktioita käytetään hyvin vähän (esim. user group)
- "exec" käytetään kun tarvitaan suoraa komentoa
- Kontrollirakenteet: yleisiä ovat if ja case
- Puppet käyttää myös omia konsepteja kuten "class" ja "anchor"

### 4.12.3.1 Dependencies Between Main Functions

- Tärkeimmät perusfunktiot:
  - package
  - file
  - service
  - user
  - group
  - exec
- Näistä voidaan rakentaa lähes kaikki konfiguraatiot
- Järjestelmän tulee olla idempotentti:
  - Muutoksia vain jos tila ei ole jo oikea
- Monet funktiot kutsuvat taustalla järjestelmän omia komentoja (esim. apt-get)
- "file" ja "exec" ovat keskeisimmät peruspalikat
- Muut funktiot rakentuvat näiden päälle
- Riippuvuudet voidaan mallintaa graafisena
- Suurin osa konfiguraatiosta voidaan tehdä pienellä määrällä perustoimintoja

## a) Räpylä. PostgreSQL käsin

#### Asennus

```bash
sudo apt-get update
sudo apt-get install postgresql
```

#### Palvelun käynnistys ja tarkistus

<img width="898" height="74" alt="image" src="https://github.com/user-attachments/assets/fa3f018a-703b-443d-aec8-766d7e1104be" />

#### Testaus

<img width="1266" height="380" alt="image" src="https://github.com/user-attachments/assets/7b9b7b3e-3168-4754-8f3d-60d365f10d77" />

#### Lopputulos

- PostgreSQL asentui onnistuneesti
- Palvelu käynnistyi oikein
- Pystyin kirjautumaan tietokantaan
- Testitietokanta luotiin onnistuneesti

## b) Automaatti PostgreSQL

Tässä tehtävässä automatisoin PostgreSQL-palvelimen asennuksen Ansiblella.

#### Projektin rakenne

Loin uuden roolin:

<img width="648" height="39" alt="image" src="https://github.com/user-attachments/assets/2777e1e2-b1ff-43a7-8a2d-9979d4a7795f" />


<img width="232" height="69" alt="image" src="https://github.com/user-attachments/assets/ac1197dc-a84d-4e00-9f20-dd427dced041" />

#### site.yml

<img width="258" height="211" alt="image" src="https://github.com/user-attachments/assets/4ebaa31e-dd49-44ef-acf4-4331c7cbcfac" />

#### roles/postgresql/tasks/main.yml

<img width="435" height="223" alt="image" src="https://github.com/user-attachments/assets/8bff335e-439c-4f2b-ae28-7f34410993de" />

#### Playbook

```bash
ansible-playbook site.yml -K
```

<img width="1211" height="165" alt="image" src="https://github.com/user-attachments/assets/c78aeb0f-2b37-4a5a-b618-fa7b77756dbd" />

#### Testaus

<img width="1263" height="481" alt="image" src="https://github.com/user-attachments/assets/0e614f33-e4ea-4386-a800-45c538396b27" />

Aikaisemmin tehty test database löytyy listasta.

## c) Asetus.

### Tavoite

- Muuttaa PostgreSQL-asetuksia herralle
- Ajaa playbook uudelleen
- Osoittaa, että asetukset tulivat käyttöön

Valitsin kaksi asetusta:

- max_connections
- log_min_duration_statement

#### Tarvittavat kansiot

<img width="681" height="57" alt="image" src="https://github.com/user-attachments/assets/3996a82b-10d9-4ca6-a421-0e24903f6837" />


<img width="242" height="97" alt="image" src="https://github.com/user-attachments/assets/d93b9861-fbcc-4f23-b2a3-12cc91cdd0c9" />

#### Asetustiedosto

```bash
micro roles/postgresql/files/ansible.conf
```
<img width="382" height="59" alt="image" src="https://github.com/user-attachments/assets/85447b5c-6369-415e-a391-187a90fd3d2e" />

#### Handler

```bash
micro roles/postgresql/handlers/main.yml
```
<img width="323" height="90" alt="image" src="https://github.com/user-attachments/assets/4550764e-9ce9-409b-a961-6636c4e5f7b9" />

#### Päivitetty tasks/main.yml

<img width="801" height="481" alt="image" src="https://github.com/user-attachments/assets/35d271ca-230e-4c55-84ce-5480d2790f85" />

##### Lyhyt huomio

Käytin komentoa:
```bash
psql -tac "SHOW config_file;"
```
- -t: tulostaa vain arvon ilman otsikoita
- -A: poistaa taulukkomuotoilun
- -c: suorittaa SQL-komennon

#### Playbookin ajo

<img width="1263" height="334" alt="image" src="https://github.com/user-attachments/assets/166601d6-f997-42ae-b213-95b0074d0366" />

#### Asetuksen tarkistus

<img width="821" height="101" alt="image" src="https://github.com/user-attachments/assets/3d6e6e4c-bb52-4fef-8c13-904e769a5bfe" />


Testasin myös lokeja:


<img width="1135" height="442" alt="image" src="https://github.com/user-attachments/assets/a8de724d-05cb-4ad0-ac09-5a4045c325ec" />

## d) Paikka remonttiin.

#### Rikotaan koko järjestelmä!

```bash
sudo apt-get purge postgresql*
```

#### Todisteet

<img width="537" height="80" alt="image" src="https://github.com/user-attachments/assets/1909618f-4429-46b4-a281-9197efb0bc6b" />

#### Ansiblella korjaaminen


<img width="1262" height="274" alt="image" src="https://github.com/user-attachments/assets/cfb92828-66a9-41ac-9b3d-153059ed8c53" />


<img width="814" height="98" alt="image" src="https://github.com/user-attachments/assets/0079199a-c544-4563-9806-dbf78b23726f" />


## e) Idempotentti

Tässä tehtävässä osoitin, että PostgreSQL- tilani on idempotentti.

Ajoin saman Ansible-playbookin uudelleen ilman että muutin mitään asetuksia.

<img width="1255" height="278" alt="image" src="https://github.com/user-attachments/assets/46386690-5445-42c5-bb25-45739426b067" />

### Mitä opin

- Idempotenssi on tärkeä osa konfiguraationhallintaa
- Sama playbook voidaan ajaa useita kertoja ilman haittaa
- Ansible kertoo idempotenssin käytännössä changed=0 tuloksella


## Lähteet:

- Karvinen 2023: [Configuration Management of Distributed Systems over Unreliable and Hostile Networks](https://westminsterresearch.westminster.ac.uk/item/w7vvz/configuration-management-of-distributed-systems-over-unreliable-and-hostile-networks) (4.12.1-4.12.3)
- [Ansible Community Docs: Handlers](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_handlers.html)
- [PostgreSQL: Setting parameters](https://www.postgresql.org/docs/current/config-setting.html)
- [Ansible Community Docs: Variables](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_variables.html)
- [Ansible Community Docs: Filters](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_filters.html)
- [PostgreSQL: File Locations](https://www.postgresql.org/docs/current/runtime-config-file-locations.html)
- [PostgreSQL: Connections and Authentication](https://www.postgresql.org/docs/current/runtime-config-connection.html)
- [PostgreSQL: Error Reporting and Logging](https://www.postgresql.org/docs/current/runtime-config-logging.html)
