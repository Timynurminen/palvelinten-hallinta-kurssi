# h5 Gitar Hero

## Lue ja tiivistä x)

### 1.3 What is git?

- Git on hajautettu versionhallintajärjestelmä, jossa jokaisella käyttäjällä on koko projektin historia lokaalisti
- Toisin kuin monet järjestelmät, Git tallentaa datan tilannekuvina, ei pelkkinä muutoksina
- Suurin osa Gitin toiminnoista on paikallisia, joten ne ovat nopeita ja toimivat myös ilman internettiä
- Git käyttää SHA-1 tarkistussummia, joten tiedon eheys on varmistettu eikä muutoksia voi tehdä huomaamatta
- Git lisää dataa, eikä yleensä poista sitä: muutosten menettäminen on vaikeaa commitin jälkeen

#### Gitin kolme tilaa

- **Modified:** tiedostoa on muutettu, mutta ei lisätty committiin
- **Staged:** tiedosto lisätty seuraavaan committiin
- **Commited:** tiedosto tallennettu Gitin tietokantaan

#### Gitin kolme aluetta
- **Working directory:** missä muokkaat tiedostoja
- **Staging area:** mitä menee seuraavaan committiin
- **Git directory:** missä Git säilyttää historian

#### Perus workflow

1. Muokkaat tiedostoja
2. Lisäät muutokset stagingiin
3. Teet commitin

#### Oma huomio

Gitin tilannekuva-ajattelu tekee siitä paljon luotettavamman kuin perinteiset järjestelmät, koska koko projetkin tila tallennetaan aina yhtenä kokonaisuutena, ei pelkkinä muutoksina.

### Git-komennon selitys

Komento: 

```bash
git add --all && git commit; git pull && git push
```

##### Osat:

- **git add --all**
  - Lisää kaikki muutokset staging-alueelle (uudet, muokatut ja poistetut tiedostot)
  - Valmistelee tiedostot seuraavaa committia varten
- **&&**
  - Suorittaa seuraavan komennon vain jos edellinen onnistuu
- **git commit**
  - Tallentaa staging-alueen muutokset Gitin historiaan (snapshot/tilannekuva)
  - Luo uuden version projektista
  - Yleensä käytetään viestin kanssa: ```bash git commmit -m "viesti" ```
- **;**
  - Erotin, suorittaa seuraavan komennon riippumatta edellisen onnistumisesta
- **git pull**
  - Hakee muutokset etävarastosta (remote repository) ja yhdistää ne paikalliseen projektiin
  - Vastaa käytännössä: ```bash git fetch + git merge ```
- **git push**
  - Lähettää paikalliset commitit etävarastoon (esim. GitHub)
  - Päivittää projektin muiden nähtäväksi

### Yhteenveto

Tämä komento tekee koko perus Git-työskentelyn:

1. Lisää muutokset
2. Tallentaa ne
3. Hakee uusimmat muutokset palvelimelta
4. Lähettää omat muutokset palvelimelle

## a) Online

Loin uuden GitHub-varaston nimellä sunshine-projekti

Varaston kuvauksessa käytin tekstiä 
"My sunshine repository for Git practice"

Valitsin varaston luonnin yhteydessä:

- README.md tiedoston
- GNU General Public License v3

Varasto luotiin onnistuneesti ja sisältää tarvittavat tiedostot.

<img width="1541" height="620" alt="image" src="https://github.com/user-attachments/assets/b3d63ead-3dd8-4cdb-88d8-735ec102d3c3" />


## b) Dolly.

Tässä tehtävässä kloonasin aiemmin luodun GitHub-varaston omalle koneelleni, tein muutoksia ja puskin ne takaisin palvelimelle.

#### Kloonaus

Kloonasin varaston ssh-osoitteella:

<img width="853" height="173" alt="image" src="https://github.com/user-attachments/assets/798a97b1-1dd3-4342-88e2-eaade0581bf5" />

#### Muutosten tekeminen

<img width="453" height="154" alt="image" src="https://github.com/user-attachments/assets/c196a7ba-dca3-41b4-b9ba-f897df458ac2" />

#### Commit ja Push

Aluksi commit epäonnistui, koska Gitin käyttäjätietoja ei ollut asennettu, joten korjasin sen.

<img width="865" height="261" alt="image" src="https://github.com/user-attachments/assets/c053d2ba-2635-4b3a-8ed0-ccfc7dc126b1" />




<img width="1014" height="44" alt="image" src="https://github.com/user-attachments/assets/fbcb3c0f-ab97-40e1-a547-c92a0a9c2f27" />



Tämän jälkeen kaikki toimi kuten piti.



<img width="832" height="400" alt="image" src="https://github.com/user-attachments/assets/91e99db1-d898-4551-b710-0911eee15d79" />




#### Lopputulos


<img width="1141" height="175" alt="image" src="https://github.com/user-attachments/assets/afa33efb-3eac-408f-af72-8dfa97435e8c" />

<img width="1472" height="372" alt="image" src="https://github.com/user-attachments/assets/5809d8e6-276b-48fb-b4c5-287f6b48e54b" />


## c) Doh!

Tein tarkoituksella huonon muutoksen README.md tiedostoon, mutta en tehnyt siitä committia

<img width="519" height="243" alt="image" src="https://github.com/user-attachments/assets/8e06a222-eff7-41cb-ae4f-dea7d1bd467e" />


Git näytti, että README.md oli muuttunut


<img width="710" height="193" alt="image" src="https://github.com/user-attachments/assets/c1abb93f-14e9-4f87-a6c2-c9d7896ff9b8" />



Poistin huonon muutoksen komennolla: 

```bash
git reset --hard
```

Ja tarkistin tilanteen uudelleen: 

```bash
git status
cat README.md
```


<img width="624" height="271" alt="image" src="https://github.com/user-attachments/assets/34bafc34-5c0c-4bc1-9f3f-4a3c996844ee" />


Huono muutos oli poistunut ja Git näytti, että työpuu oli siisti.

#### Huomio

```bash
git reset --hard
```
Poistaa committoimattomat muutokset, eikä niitä saa normaalisti enää takaisin.

## d) Tukki.

Tarkastelin varaston commit-historiaa komennolla:
```bash
git log
```
Komento näyttää jokaisesta commitista tunnisteen, tekijän nimen, s-postin, ajankohdan ja commit-viestin.



<img width="877" height="234" alt="image" src="https://github.com/user-attachments/assets/f308419b-c38d-4b63-866b-31bd092ec1e7" />

Tämän lisäksi tarkistelin tarkemmin tiedostoihin tehtyjä muutoksia komennolla
```bash
git log --patch
```
Tämä komento näyttää commit-historian lisäksi myös rivikohtaiset muutokset tiedostoihin. Esimerkiksi README.md-tiedostoon lisätyt rivit näkyvät vihreällä +-merkillä, mikä helpottaa muutosten hahmottamista.

Kuvasta näkee, että lisäsin README.md tiedostoon uuden "Local update"-osion ja tekstirivin virtuaalikoneelta tehdystä muutoksesta.



<img width="905" height="633" alt="image" src="https://github.com/user-attachments/assets/c77434c4-5f43-4111-bedf-f606907ce41a" />


Tarkistin myös Gitin käyttäjätiedot:
```bash
git config --global user.name
git config --global user.email
```
Nimi ja s-postiosoite näkyivät oikein commit-historiassa, joten niitä ei tarvinnut muokata.


<img width="759" height="81" alt="image" src="https://github.com/user-attachments/assets/6d2dccab-86ca-4d74-9a1a-d0d03d4a3c8b" />


### Lopputulos

- Commit-historia toimii oikein
- Muutokset tiedostoihin ovat nähtävissä selkeästi
- Käyttäjätiedot näkyvät Commitissa oikein

## e) Gitanbile.

Laitoin Ansible-kansion versionhallintaan

```bash
cd ansible
git init
```

Lisäksi tein .gitignore-tiedoston, jotta turhia tiedostoja ei tallenneta versionhallintaan.

Tein pienen muutoksen site.yml-tiedostoon lisäämällä kommentin.

```yaml
# Ansible config managed with Git
```


<img width="433" height="251" alt="image" src="https://github.com/user-attachments/assets/75d36bae-06e1-4184-8a70-3b2d69f3c0b3" />



Ajoin Ansible-playbookin muutoksen jälkeen:

```bash
ansible-playbook site.yml -K
```

<img width="1248" height="56" alt="image" src="https://github.com/user-attachments/assets/c13b1f85-a11f-46ee-b376-058bb1a539b9" />


Muokkausten jälkeen katsoin statuksen:


<img width="770" height="261" alt="image" src="https://github.com/user-attachments/assets/e3b2148f-8b7b-4517-bec2-980480d94966" />


Lopuksi tallensin version:

```bash
git add --all
git commit -m "Track Ansible Configuration"
```


<img width="782" height="456" alt="image" src="https://github.com/user-attachments/assets/130d1414-62ab-4ec4-9fed-c9ab3f6f082a" />



## f) Pari

Pari hankittu.
