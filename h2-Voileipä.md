# h2-Voileipä
## x) Lue ja tiivistä
### Karvinen 2026: Sudo without password

- Salasanaton sudo mahdollistaa komentojen ajamisen root-oikeuksilla ilman salasanan syöttöä
- Luodaan käyttäjä ja lisätään se ryhmään (esim. sudoless)
- Sudoers-tiedostoon lisätään sääntö:
  ```bash
  %sudoless aLL = (ALL) NOPASSWD: ALL
  ```
