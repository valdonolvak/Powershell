Vabandust, et eelmine vastus ei olnud piisavalt põhjalik. Siin on nüüd **Peatükk 6** täielik ja terviklik materjal, kuhu on kokku põimitud mõlema teksti parimad osad, koodinäited ja harjutused.

---

# Peatükk 6: Active Directory (AD) moodul

Väga hea! Nüüd siseneme PowerShelli kõige võimsamasse ja IT-administraatorite jaoks kõige olulisemasse valdkonda – **Active Directory (AD)**. See on PowerShelli "kuninglik" peatükk.

Active Directory moodul võimaldab hallata tuhandeid kasutajaid, arvuteid ja gruppe vaid mõne koodireaga. Kui varem tegid asju ühes arvutis, siis nüüd hakkad haldama tervet ettevõtte võrku.

### 1. Mis on AD moodul ja kuidas seda laadida?

Active Directory moodul on käskude kogum, mis räägib domeenikontrolleriga. Et PowerShell oskaks AD-ga rääkida, peab olema installitud **RSAT** (Remote Server Administration Tools).

* **Mooduli laadimine:** `Import-Module ActiveDirectory`
* **Käskude otsimine:** `Get-Command -Module ActiveDirectory` (näed kõiki AD-ga seotud käske).

### 2. AD objektide loogika ja hierarhia

Active Directorys on kolm peamist objekti, millega me tegeleme:

1. **Users** (Kasutajad)
2. **Groups** (Grupid)
3. **Computers** (Arvutid)

Need objektid asuvad **OU**-des (Organizational Units) ehk organisatsiooniüksustes, mis on nagu kaustad AD puus, võimaldades struktureerida ettevõtte ressursse.

### 3. Põhilised AD cmdletid

AD käsud on väga loogilised: nad järgivad `Verb-Noun` loogikat, kuid nimisõna ees on alati eesliide **AD**.

* **Get-ADUser** – Kasutajate info pärimine.
* **New-ADUser** – Uue kasutaja loomine.
* **Set-ADUser** – Kasutaja andmete muutmine (nt ametinimetus).
* **Get-ADGroup** – Gruppide leidmine.
* **Add-ADGroupMember** – Kasutaja lisamine gruppi.

#### A. Kasutajate info pärimine (Get-ADUser)

Vaikimisi näitab see vaid põhiinfot. Kui soovid näha kõiki andmeid (nt e-mail, osakond), pead kasutama parameetrit `-Properties`.

**Koodinäide:**

```powershell
# Leia kasutaja "juhan.tamm" ja näita tema e-maili ning osakonda
Get-ADUser -Identity "juhan.tamm" -Properties EmailAddress, Department | Select-Object Name, EmailAddress, Department

# Leia kõik kasutajad, kelle eesnimi on "Mari"
Get-ADUser -Filter "GivenName -eq 'Mari'"

# Kõikide omaduste nägemine (ettevaatust, andmeid on palju!)
Get-ADUser -Identity "juhan.tamm" -Properties *

```

#### B. Gruppide haldus (Get-ADGroup, Add-ADGroupMember)

**Koodinäide:**

```powershell
# Vaata, kes kuuluvad gruppi "IT-Osakond"
Get-ADGroupMember -Identity "IT-Osakond" | Select-Object Name, SamAccountName

# Lisa kasutaja uude gruppi
Add-ADGroupMember -Identity "Kaugtoo_Kasutajad" -Members "juhan.tamm"

```

#### C. Arvutite info (Get-ADComputer)

**Koodinäide:**

```powershell
# Leia kõik arvutid, mille operatsioonisüsteem on Windows 11
Get-ADComputer -Filter "OperatingSystem -like '*Windows 11*'" -Properties OperatingSystem | Select-Object Name, OperatingSystem

```

### 4. Filtreerimine AD-s: Filter vs Where-Object

AD moodulis on kriitiliselt oluline kasutada parameetrit **`-Filter`**. See on "serveripoolne" filtreerimine, mis tähendab, et domeenikontroller saadab sulle ainult need andmed, mida palusid. See on palju kiirem kui andmete "torutamine" `Where-Object` kätte.

**Koodinäide:**

```powershell
# ÕIGE ja kiire (serveripoolne):
Get-ADUser -Filter "Enabled -eq 'True'"

# VALE ja aeglane (klindipoolne):
Get-ADUser -Filter * | Where-Object {$_.Enabled -eq $true}

```

---

### Iseseisvad harjutused (AD baaskäsud)

1. **Harjutus: Kasutaja leidmine.** Leia oma domeenist kasutaja, kelle perekonnanimi on "Kask" (või "Tamm"). Kuva tema `SamAccountName`, `EmailAddress` ja `LastLogonDate`.
2. **Harjutus: Grupi audit.** Vali üks grupp (nt "Administraatorid" või "IT-osakond") ja kuva ekraanile nimekiri selle grupi liikmetest.
3. **Harjutus: Lukus kontod.** Otsi üles kõik kasutajad, kelle konto on parooli valesti sisestamise tõttu lukus. *Vihje: kasuta parameetrit `-Filter 'LockedOut -eq $true'`.*
4. **Harjutus: Arvutite loetelu.** Leia kõik arvutid, mis on op-süsteemiga "Windows 11" ja kuva nende nimed.

---

### 5. Masshaldus (AD + Pipeline)

PowerShelli tõeline võim avaldub siis, kui ühendame eelmiste peatükkide teadmised (objektid ja tsüklid) AD mooduliga.

**Näide: Kõikide ühe osakonna kasutajate lisamine gruppi:**

```powershell
$kasutajad = Get-ADUser -Filter 'Department -eq "Raamatupidamine"'
foreach ($isik in $kasutajad) {
    Add-ADGroupMember -Identity "Majandustarkvara_Kasutajad" -Members $isik
    Write-Host "Kasutaja $($isik.Name) lisati gruppi." -ForegroundColor Green
}

```

---

### Iseseisev praktiline töö: "Automaatne kasutajate audit"

**Ülesanne:** Administraator peab koostama raporti kasutajate kohta, kes pole ammu süsteemi sisse loginud, ning nad turvalisuse huvides deaktiveerima.

**Töö käik ja nõuded:**

1. **Otsing:** Leia kõik Active Directory kasutajad, kes pole sisse loginud viimase **90 päeva** jooksul.
* *Vihje:* Kasuta ajapiiri määramiseks: `$Aeg = (Get-Date).AddDays(-90)`.


2. **Filtreerimine:** Välista otsingust admin-kontod (nt kus nimi sisaldab "Admin").
3. **Tegevus ja Logimine:**
* Loo skript, mis käib need kasutajad `foreach` tsükliga läbi.
* Skript peab iga kasutaja kohta lisama logifaili `AD_Audit.log` kirje: `[$(Get-Date)] KASUTAJA: $($isik.Name) - Staatus: Aegunud - Konto deaktiveerimine`.
* Deaktiveeri need kasutajad käsuga `Disable-ADAccount`.


4. **Väljund:** Kuva ekraanile tabelina nende kasutajate nimed (`Name`) ja viimane sisselogimise aeg (`LastLogonDate`).

**Esitamiseks õpetajale:**

* Skriptifail (`.ps1`).
* **Oluline selgitus:** Selgita, miks on Active Directory puhul parameeter `-Filter` parem valik kui `Where-Object` kasutamine torustikus ning mis vahe on `-Identity` ja `-Filter` parameetritel.
* Ekraantõmmis logifailist `AD_Audit.log`.

---

**Õpetaja märkus:** Kui sul ei ole ligipääsu AD serverile, saad kasutada käsku `Get-ADUser -Filter * -WhatIf`. See näitab sulle, mida skript teeks, ilma et ta tegelikult ühtegi kontot muudaks!

---
