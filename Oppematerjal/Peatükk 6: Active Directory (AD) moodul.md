See on PowerShelli "kuninglik" peatükk. IT-süsteemide administraatori jaoks on **Active Directory (AD)** moodul peamine tööriist, millega hallata tuhandeid kasutajaid ja arvuteid vaid sekunditega.

---

## Peatükk 6: Active Directory (AD) moodul

### 1. Mooduli laadimine ja ettevalmistus

Et PowerShell oskaks AD-ga rääkida, peab olema installitud **RSAT** (Remote Server Administration Tools).

* Mooduli importimine: `Import-Module ActiveDirectory`
* Kontroll, kas käsud on olemas: `Get-Command -Module ActiveDirectory`

### 2. AD objektide hierarhia

AD-s on kolm peamist objekti tüüpi, mida me hallata saame:

1. **Users** (Kasutajad)
2. **Groups** (Grupid)
3. **Computers** (Arvutid)

Kõik need asuvad **OU**-des (Organizational Units) ehk organisatsiooniüksustes.

### 3. Peamised käsud (Cmdletid)

AD käsud järgivad sama loogikat nagu varem, kuid nimisõna ees on eesliide **AD**.

* **Get-ADUser** – Kasutajate info leidmine.
* **New-ADUser** – Uue kasutaja loomine.
* **Set-ADUser** – Kasutaja andmete muutmine (nt parooli aegumine, ametinimetus).
* **Get-ADGroup** – Gruppide leidmine.
* **Add-ADGroupMember** – Kasutaja lisamine gruppi.

### 4. Filter ja Properties

AD objektidel on sadu omadusi (Properties), mida vaikimisi ei näidata.

* **Kõikide omaduste nägemine:** `Get-ADUser -Identity "juhan" -Properties *`
* **Filtreerimine:** AD-s on soovitatav kasutada serveripoolset filtrit:
`Get-ADUser -Filter 'Name -like "Jaan*"'` (See on kiirem kui `Where-Object`).

---

### Iseseisvad harjutused (AD baaskäsud)

1. **Harjutus: Kasutaja otsing.** Leia oma AD-st kasutaja, kelle perekonnanimi on "Tamm". Kuva tema `EmailAddress` ja `LastLogonDate`.
2. **Harjutus: Grupi liikmed.** Leia grupp nimega "IT-osakond" ja kuva kõik selle grupi liikmed (`Get-ADGroupMember`).
3. **Harjutus: Lukus kontod.** Koosta käsurida, mis leiab kõik kasutajakontod, mis on lukus. *Vihje: kasuta parameetrit `-LockedOut*`.
4. **Harjutus: Arvutite loetelu.** Leia kõik arvutid, mis on op-süsteemiga "Windows 11" ja kuva nende nimed.

---

### 5. Masshaldus (AD + Pipeline)

PowerShelli tõeline võim avaldub siis, kui ühendame eelmiste peatükkide teadmised (objektid ja tsüklid) AD mooduliga.

**Näide: Kõikide ühe osakonna kasutajate lisamine gruppi:**

```powershell
$kasutajad = Get-ADUser -Filter 'Department -eq "Raamatupidamine"'
foreach ($isik in $kasutajad) {
    Add-ADGroupMember -Identity "Majandustarkvara_Kasutajad" -Members $isik
}

```

---

### Iseseisev praktiline töö: "Automaatne kasutajate audit"

**Ülesanne:** Administraator peab koostama raporti kasutajate kohta, kes pole ammu süsteemi sisse loginud, ning nad turvalisuse huvides deaktiveerima.

**Töö käik ja nõuded:**

1. **Otsing:** Leia kõik Active Directory kasutajad, kes pole sisse loginud viimase **90 päeva** jooksul.
* *Vihje:* Kasuta muutuja loomiseks `(Get-Date).AddDays(-90)`.


2. **Filtreerimine:** Välista otsingust admin-kontod (nt kus nimi sisaldab "Admin").
3. **Tegevus ja Logimine:**
* Loo skript, mis käib need kasutajad `foreach` tsükliga läbi.
* Skript peab iga kasutaja kohta lisama logifaili `AD_Audit.log` kirje: "[Kuupäev] KASUTAJA: [Nimi] - Staatus: Aegunud - Konto deaktiveerimine".
* Deaktiveeri need kasutajad käsuga `Disable-ADAccount`.


4. **Väljund:** Kuva ekraanile tabelina nende kasutajate nimed (`Name`) ja viimane sisselogimise aeg (`LastLogonDate`).

**Esitamiseks õpetajale:**

* Skriptifail (`.ps1`).
* **Oluline selgitus:** Selgita, miks on Active Directory puhul parameeter `-Filter` parem valik kui `Where-Object` kasutamine torustikus.
* Ekraanitõmmis logifailist (võid kasutada testandmeid/muutujaid, kui sul puudub ligipääs päris serverile).

---

**Õpetaja märkus:** Kui sul ei ole ligipääsu AD serverile, saad kasutada käsku `Get-ADUser -Filter * -WhatIf`. See näitab sulle, mida skript teeks, ilma et ta tegelikult ühtegi kontot deaktiveeriks!
