
1️⃣ **Õpiülesanne (kirjeldus õpilasele)**
2️⃣ **CSV fail**

---

# 1️⃣ ÕPIÜLESANNE

## Active Directory kasutajate loomine PowerShelli ja CSV abil

### 🎯 Ülesande eesmärk

Õpib looma **Active Directory kasutajaid automaatselt** PowerShelli skripti abil, kasutades sisendandmetena CSV faili.
Ülesanne arendab oskusi:

* AD kasutajate haldamisel
* OU-de loomisel
* CSV-failide töötlemisel
* PowerShelli automatiseerimisel

---

### 📋 Ülesande kirjeldus

Koosta PowerShelli skript, mis:

1. Loeb kasutajate andmed CSV-failist
2. Lisab kasutajad **Active Directory domeeni**
3. Paigutab kasutajad õigesse **OU-sse**
4. Loob OU automaatselt, **kui see ei eksisteeri**
5. Määrab kasutajatele vaikimisi parooli CSV-failist
6. Sunnib kasutajaid **esimesel sisselogimisel parooli vahetama**
7. Loob ainult **aktiivsed kasutajakontod**

---

### 🏗️ Nõuded

* Domeen: `TEST.LOCAL` 
  👉 *Iga õppija peab vajadusel muutma skriptis domeeni nime*
* Peamine OU: **KASUTAJAD**
* Alam-OU-d:

  * IT
  * STAFF
  * ÕPETAJAD
  * ÕPILASED
  * RAAMATUPIDAMINE
* Kokku **50 kasutajat**

  * 10 kasutajat igas OU-s
* Kasutajanimi peab olema kujul:
  **eesnimi.perenimi**
* Parool: määratud CSV-failis
* Kasutaja peab olema **aktiivne**

---

### 🛠️ Eeldused

* Skript käivitatakse **Domain Controlleris** või masinas, kus on paigaldatud **RSAT**
* PowerShelli käivitamine administraatori õigustes
* ActiveDirectory moodul on saadaval

---

# 2️⃣ CSV FAIL (failinimeks anda kasutajad.csv)

## Kasutajate andmed

📄 **Failinimi:** `users.csv`
📌 **Kodeering:** UTF-8
📌 **Eraldaja:** koma `,`

```csv
FirstName,LastName,OU,Password
Markus,Tamm,IT,Passw0rd
Kaur,Kask,IT,Passw0rd
Laura,Mets,IT,Passw0rd
Rasmus,Lepp,IT,Passw0rd
Anneli,Saar,IT,Passw0rd
Martin,Põld,IT,Passw0rd
Kristjan,Kuusk,IT,Passw0rd
Helen,Ojamaa,IT,Passw0rd
Siim,Rand,IT,Passw0rd
Grete,Allik,IT,Passw0rd

Jaan,Parts,STAFF,Passw0rd
Mari,Lill,STAFF,Passw0rd
Peeter,Sepp,STAFF,Passw0rd
Liis,Kivi,STAFF,Passw0rd
Andres,Kalju,STAFF,Passw0rd
Kätlin,Rahu,STAFF,Passw0rd
Toomas,Vaher,STAFF,Passw0rd
Pille,Kalda,STAFF,Passw0rd
Raul,Hallik,STAFF,Passw0rd
Eve,Kangur,STAFF,Passw0rd

Tiit,Õun,ÕPETAJAD,Passw0rd
Kadri,Kool,ÕPETAJAD,Passw0rd
Aivar,Õpik,ÕPETAJAD,Passw0rd
Maris,Tahvel,ÕPETAJAD,Passw0rd
Rene,Kriit,ÕPETAJAD,Passw0rd
Sirje,Klass,ÕPETAJAD,Passw0rd
Urmas,Tund,ÕPETAJAD,Passw0rd
Heli,Aine,ÕPETAJAD,Passw0rd
Indrek,Hinne,ÕPETAJAD,Passw0rd
Annika,Kava,ÕPETAJAD,Passw0rd

Kevin,Noor,ÕPILASED,Passw0rd
Sandra,Koolik,ÕPILASED,Passw0rd
Karl,Uus,ÕPILASED,Passw0rd
Mia,Väli,ÕPILASED,Passw0rd
Robin,Kivi,ÕPILASED,Passw0rd
Laura,Pink,ÕPILASED,Passw0rd
Erik,Muru,ÕPILASED,Passw0rd
Sofia,Rohe,ÕPILASED,Passw0rd
Marko,Paju,ÕPILASED,Passw0rd
Emma,Tee,ÕPILASED,Passw0rd

Riina,Arve,RAAMATUPIDAMINE,Passw0rd
Tiina,Maks,RAAMATUPIDAMINE,Passw0rd
Kaja,Palk,RAAMATUPIDAMINE,Passw0rd
Marek,Kulu,RAAMATUPIDAMINE,Passw0rd
Sirli,Tulu,RAAMATUPIDAMINE,Passw0rd
Janek,Bilanss,RAAMATUPIDAMINE,Passw0rd
Helin,Raamat,RAAMATUPIDAMINE,Passw0rd
Paavo,Konto,RAAMATUPIDAMINE,Passw0rd
Anu,Kreedit,RAAMATUPIDAMINE,Passw0rd
Risto,Deebet,RAAMATUPIDAMINE,Passw0rd
```

---

## ✅ Kokkuvõte

Selle ülesande lahendamisel:

* loodi **50 AD kasutajat**
* loodi **OU struktuur automaatselt**
* kasutati **CSV-faili sisendina**
* rakendati **turvanõuet (parooli vahetus)**
